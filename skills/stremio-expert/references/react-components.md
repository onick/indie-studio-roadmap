# React Components System — Reference

## Estructura de un Componente

Cada componente vive en su propia carpeta:

```
src/components/Button/
├── Button.js         # Componente React
├── styles.less       # Estilos Less
└── index.js          # Re-export
```

### index.js (barrel export)

```javascript
const Button = require('./Button');
module.exports = Button;
```

### Componente tipico

```javascript
const React = require('react');
const PropTypes = require('prop-types');
const classnames = require('classnames');
const styles = require('./styles.less');

const Button = ({ className, label, icon, disabled, onClick }) => {
    return (
        <button
            className={classnames(styles['button'], className, {
                [styles['disabled']]: disabled
            })}
            tabIndex={disabled ? -1 : 0}
            onClick={onClick}
            title={label}
        >
            {icon && <img className={styles['icon']} src={icon} alt="" />}
            {label && <span className={styles['label']}>{label}</span>}
        </button>
    );
};

Button.propTypes = {
    className: PropTypes.string,
    label: PropTypes.string,
    icon: PropTypes.string,
    disabled: PropTypes.bool,
    onClick: PropTypes.func
};

module.exports = Button;
```

### Less Styling

```less
// styles.less
@import (reference) '~stremio-colors/less/stremio-colors.less';
@import (reference) '~stremio/common/screen-sizes.less';

.button {
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 0.25rem;
    background-color: @color-surface-light-5;
    color: @color-primary-text;
    cursor: pointer;
    outline: none;

    &:hover, &:focus {
        background-color: @color-surface-light-10;
    }

    &.disabled {
        opacity: 0.5;
        pointer-events: none;
    }

    .icon {
        width: 1.5rem;
        height: 1.5rem;
        margin-right: 0.5rem;
    }

    .label {
        font-size: 0.875rem;
        white-space: nowrap;
    }
}
```

## Catalogo de Componentes (32)

### Input Components

| Componente | Props clave | Uso |
|-----------|-------------|-----|
| `Button` | label, icon, disabled, onClick | Boton generico |
| `Checkbox` | checked, onChange | Toggle binario |
| `Toggle` | checked, onChange | Switch on/off |
| `RadioButton` | selected, value, onChange | Seleccion unica |
| `TextInput` | value, placeholder, onChange | Input de texto |
| `NumberInput` | value, min, max, onChange | Input numerico |
| `ColorInput` | value, onChange | Selector de color |
| `Slider` | value, min, max, step, onChange | Slider continuo |
| `SearchBar` | value, onChange | Barra de busqueda |
| `Chips` | items, selected, onSelect | Tags seleccionables |

### Media Components

| Componente | Props clave | Uso |
|-----------|-------------|-----|
| `Image` | src, fallback, alt | Imagen con fallback |
| `Video` | src, stream | Reproductor de video |
| `MetaItem` | meta, compact | Card de pelicula/serie |
| `MetaPreview` | meta | Preview expandido |
| `MetaRow` | title, items, onMore | Fila horizontal de items |
| `LibItem` | libItem | Item de la library |
| `ContinueWatchingItem` | libItem, progress | Item con progreso |

### Layout Components

| Componente | Props clave | Uso |
|-----------|-------------|-----|
| `NavBar` | items, active | Barra de navegacion |
| `MainNavBars` | route | Navegacion principal + lateral |
| `HorizontalScroll` | children | Scroll horizontal |
| `BottomSheet` | open, onClose | Sheet desde abajo (mobile) |

### Overlay Components

| Componente | Props clave | Uso |
|-----------|-------------|-----|
| `ModalDialog` | open, onClose, title | Modal generico |
| `ContextMenu` | items, position | Menu contextual |
| `Popup` | open, position | Popup posicional |
| `Multiselect` | options, selected | Multi-select dropdown |
| `MultiselectMenu` | options, selected | Menu multi-select |
| `AddonDetailsModal` | addon | Detalles de addon |
| `EventModal` | event | Modal de evento |
| `SharePrompt` | url | Share dialog |

### Utility Components

| Componente | Props clave | Uso |
|-----------|-------------|-----|
| `Transition` | children, duration | Animacion enter/exit |
| `DelayedRenderer` | delay, children | Render con delay |
| `ShortcutsGroup` | shortcuts | Grupo de keyboard shortcuts |

## Patrones de Componentes

### classnames para clases condicionales

```javascript
const classnames = require('classnames');

<div className={classnames(styles['container'], className, {
    [styles['active']]: isActive,
    [styles['compact']]: compact,
    [styles['focused']]: focused
})} />
```

### prop-types para documentacion

```javascript
Component.propTypes = {
    className: PropTypes.string,
    title: PropTypes.string.isRequired,
    items: PropTypes.arrayOf(PropTypes.shape({
        id: PropTypes.string.isRequired,
        name: PropTypes.string
    })),
    onSelect: PropTypes.func
};
```

### Spatial Navigation

```javascript
// tabIndex para navegabilidad
<div tabIndex={0} className={styles['focusable']}>
  {/* contenido */}
</div>

// Focus management
const containerRef = React.useRef();
React.useEffect(() => {
    if (focused) {
        containerRef.current?.focus();
    }
}, [focused]);
```

### Responsive (screen-sizes.less)

```less
@import (reference) '~stremio/common/screen-sizes.less';

.container {
    padding: 1rem;

    @media @screen-small {
        padding: 0.5rem;
    }

    @media @screen-medium {
        padding: 0.75rem;
    }
}
```

## Colores (stremio-colors)

```less
@import (reference) '~stremio-colors/less/stremio-colors.less';

// Colores principales
@color-background               // Fondo principal (oscuro)
@color-surface                   // Superficies
@color-surface-light-5           // Surface +5% luz
@color-surface-light-10          // Surface +10% luz
@color-primary                   // Color primario (acento)
@color-primary-text              // Texto sobre primary
@color-secondary-text            // Texto secundario
@color-placeholder-text          // Texto placeholder
@color-signal-success            // Verde
@color-signal-warning            // Amarillo
@color-signal-error              // Rojo
```

## Import Pattern

El proyecto usa `require()` (CommonJS), NO ES modules:

```javascript
// Correcto
const React = require('react');
const { useTranslation } = require('react-i18next');
const styles = require('./styles.less');

// Incorrecto (NO usar)
import React from 'react';
```

**Excepcion**: Archivos TypeScript (`.ts`/`.tsx`) SI usan ES modules (`import/export`).

## Path Alias

```javascript
// tsconfig.json y webpack definen alias 'stremio/*' → 'src/*'
const { useModelState } = require('stremio/common');
const { Button, Image } = require('stremio/components');
```
