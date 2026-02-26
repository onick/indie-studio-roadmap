# Testing & i18n — Reference

## Testing (Jest 29)

### Setup

```bash
pnpm test              # Correr todos los tests
pnpm run scan-translations   # Solo scan de traducciones
```

### Tests existentes (3)

| Test | Proposito |
|------|-----------|
| `tests/copyright.spec.js` | Valida headers de copyright en archivos fuente |
| `tests/i18nScan.test.js` | Detecta traducciones faltantes/no usadas |
| `tests/routesRegexp.spec.js` | Valida que regex de rutas matchean URLs esperadas |

### Tests de Routes

```javascript
// tests/routesRegexp.spec.js
const routesRegexp = require('../src/common/routesRegexp');

describe('routesRegexp', () => {
    test('board matches /', () => {
        expect('/').toMatch(routesRegexp.board);
    });

    test('board matches /board', () => {
        expect('/board').toMatch(routesRegexp.board);
    });

    test('discover matches with params', () => {
        expect('/discover/movie/top').toMatch(routesRegexp.discover);
        const match = '/discover/movie/top'.match(routesRegexp.discover);
        expect(match[1]).toBe('movie');
        expect(match[2]).toBe('top');
    });

    test('metadetails matches both paths', () => {
        expect('/metadetails/movie/tt123').toMatch(routesRegexp.metadetails);
        expect('/detail/movie/tt123').toMatch(routesRegexp.metadetails);
    });
});
```

### Escribir Tests Nuevos

```javascript
// tests/myComponent.spec.js
const React = require('react');
const { render, screen, fireEvent } = require('@testing-library/react');
const MyComponent = require('../src/components/MyComponent');

describe('MyComponent', () => {
    test('renders with label', () => {
        render(<MyComponent label="Click me" />);
        expect(screen.getByText('Click me')).toBeTruthy();
    });

    test('calls onClick when clicked', () => {
        const onClick = jest.fn();
        render(<MyComponent label="Click" onClick={onClick} />);
        fireEvent.click(screen.getByText('Click'));
        expect(onClick).toHaveBeenCalledTimes(1);
    });
});
```

### Gap de Testing

El proyecto tiene un **gap significativo** en testing:
- No hay tests de componentes React
- No hay tests de hooks
- No hay tests de integracion
- No hay tests del Core service bridge
- Solo tests de infraestructura (copyright, i18n, routes regex)

Recomendacion: Priorizar tests para `useModelState`, rutas criticas, y componentes core.

## i18n (i18next)

### Setup

```javascript
// src/index.js
const i18next = require('i18next');
const { initReactI18next } = require('react-i18next');
const translations = require('stremio-translations');

i18next
    .use(initReactI18next)
    .init({
        resources: translations,
        lng: 'en',
        fallbackLng: 'en',
        interpolation: {
            escapeValue: false
        }
    });
```

### Usar traducciones en componentes

```javascript
const { useTranslation } = require('react-i18next');

function MyComponent() {
    const { t } = useTranslation();

    return (
        <div>
            <h1>{t('DISCOVER')}</h1>
            <p>{t('NO_ITEMS_FOUND')}</p>
            <Button label={t('INSTALL_ADDON')} />
        </div>
    );
}
```

### Hook useTranslate (custom)

```javascript
const { useTranslate } = require('stremio/common');

function MyComponent() {
    const translate = useTranslate();
    // Wrapper sobre useTranslation con funcionalidad extra
}
```

### Translation Keys

Las keys estan en UPPER_SNAKE_CASE:

```
DISCOVER
LIBRARY
SETTINGS
SEARCH
PLAYER
ADDONS
NO_ITEMS_FOUND
INSTALL_ADDON
UNINSTALL_ADDON
CONTINUE_WATCHING
SHARE
```

### stremio-translations

Paquete externo que contiene todas las traducciones:

```
stremio-translations/
├── en.json
├── es.json
├── fr.json
├── de.json
├── ... (30+ idiomas)
```

### Translation Scanning

```bash
# Detecta strings hardcodeados y keys faltantes
pnpm run scan-translations
```

El scan (`tests/i18nScan.test.js`):
1. Escanea todos los archivos fuente
2. Busca strings que deberian ser keys de traduccion
3. Verifica que todas las keys usadas existen en las traducciones
4. Detecta keys definidas pero no usadas

### Reglas de i18n

1. **NUNCA** hardcodear strings visibles al usuario
2. Usar `t('KEY')` para todas las strings
3. Keys en UPPER_SNAKE_CASE
4. Correr scan despues de cada cambio
5. Si agregas una key nueva, solo necesitas agregarla en `en.json` — el fallback se encarga del resto

### Formato de traducciones

```json
{
  "DISCOVER": "Discover",
  "SEARCH_PLACEHOLDER": "Search for movies, series...",
  "ITEMS_COUNT": "{{count}} items",
  "LAST_WATCHED": "Last watched: {{date}}"
}
```

### Interpolacion

```javascript
t('ITEMS_COUNT', { count: 42 })
// → "42 items"

t('LAST_WATCHED', { date: formattedDate })
// → "Last watched: Feb 26, 2026"
```

## ESLint

### Configuracion clave

```javascript
// eslint.config.mjs
export default [
    {
        rules: {
            'indent': ['error', 4],              // 4 espacios
            'quotes': ['error', 'single'],        // Single quotes
            'semi': ['error', 'always'],           // Semicolons
            'eqeqeq': ['error', 'always'],        // ===
            'no-console': ['error', { allow: ['warn', 'error'] }],
            'no-unused-vars': ['error', { argsIgnorePattern: '^_' }]
        }
    },
    // TypeScript overrides
    {
        files: ['**/*.ts', '**/*.tsx'],
        rules: {
            '@typescript-eslint/no-explicit-any': 'off',
            '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }]
        }
    }
];
```

### Correr linting

```bash
pnpm lint              # Lint todo src/
pnpm lint -- --fix     # Auto-fix
```
