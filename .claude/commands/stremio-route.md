# /stremio-route — Crear o Modificar Rutas

Eres el **Stremio Web Expert**. Crea o modifica rutas del hash router.

## Instrucciones

### Uso

```
/stremio-route create [name] [path]   — Crear ruta nueva
/stremio-route modify [name]          — Modificar ruta existente
/stremio-route list                   — Listar rutas con paths
```

### Create — Crear Ruta

1. Agrega regex a `src/common/routesRegexp.js`.
2. Crea estructura:

```
src/routes/[Name]/
├── [Name].js
├── styles.less
└── index.js
```

3. El componente de ruta DEBE:
   - Usar `CoreSuspender` si necesita datos del WASM core
   - Envolver en `MainNavBars` si necesita navegacion
   - Extraer `urlParams` y `queryParams`
   - Usar `useModelState` para datos del core

**Estructura base:**
```javascript
const React = require('react');
const classnames = require('classnames');
const { useModelState, CoreSuspender } = require('stremio/common');
const { MainNavBars } = require('stremio/components');
const styles = require('./styles.less');

const [Name]Content = ({ urlParams }) => {
    const [param1] = urlParams;

    const data = useModelState({
        model: '[Model]',
        action: 'Load',
        args: { /* args basados en params */ }
    });

    return (
        <div className={styles['content']}>
            {/* renderizar data */}
        </div>
    );
};

const [Name] = ({ urlParams, queryParams }) => {
    return (
        <MainNavBars className={styles['[name]-container']} route={'[name]'}>
            <React.Suspense fallback={<div className={styles['loading']} />}>
                <CoreSuspender>
                    <[Name]Content urlParams={urlParams} />
                </CoreSuspender>
            </React.Suspense>
        </MainNavBars>
    );
};

module.exports = [Name];
```

4. Registra en `src/routes/index.js`.
5. Si la ruta necesita proteccion (auth), agregala al config de protected routes.

### Modify — Modificar Ruta

1. Lee TODOS los archivos de la ruta.
2. Identifica los modelos del core que usa.
3. Verifica que `urlParams` se extraen correctamente.
4. Aplica modificacion.
5. Verifica que el regex sigue matcheando.

### List — Listar Rutas

Lee `routesRegexp.js` y muestra tabla:

```
| Ruta | Path | Params | Protected |
|------|------|--------|-----------|
| board | / or /board | none | no |
| discover | /discover/:transport?/:type?/:catalog? | 3 | no |
| ...
```

### Reglas
- **SIEMPRE** agregar regex en `routesRegexp.js`.
- **SIEMPRE** usar `CoreSuspender` si la ruta necesita datos del core.
- **SIEMPRE** usar `MainNavBars` para layout consistente.
- **NUNCA** importar React Router.
- Navegacion via `window.location.hash = '#/path'`.
- Deep equality check en route params para evitar re-renders.
- Focus management (RouteFocusedProvider) para spatial nav.
