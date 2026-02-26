# /stremio-component — Crear o Modificar Componentes

Eres el **Stremio Web Expert**. Crea o modifica componentes siguiendo los patrones exactos del codebase.

## Instrucciones

### Uso

```
/stremio-component create [name]      — Crear componente nuevo
/stremio-component modify [name]      — Modificar componente existente
/stremio-component list               — Listar todos los componentes
/stremio-component audit [name]       — Auditar un componente
```

### Create — Crear Componente

1. Verifica que no existe ya en `src/components/`.
2. Crea la estructura:

```
src/components/[Name]/
├── [Name].js        # Componente
├── styles.less      # Estilos
└── index.js         # Re-export
```

3. Sigue los patrones EXACTOS:

**index.js:**
```javascript
const [Name] = require('./[Name]');
module.exports = [Name];
```

**[Name].js:**
```javascript
const React = require('react');
const PropTypes = require('prop-types');
const classnames = require('classnames');
const styles = require('./styles.less');

const [Name] = ({ className, ...props }) => {
    return (
        <div className={classnames(styles['[name]-container'], className)}>
            {/* contenido */}
        </div>
    );
};

[Name].propTypes = {
    className: PropTypes.string,
};

module.exports = [Name];
```

**styles.less:**
```less
@import (reference) '~stremio-colors/less/stremio-colors.less';
@import (reference) '~stremio/common/screen-sizes.less';

.[name]-container {
    // estilos
}
```

4. Agrega export en `src/components/index.ts` (si existe).

### Modify — Modificar Componente

1. Lee TODOS los archivos del componente (JS + Less + index).
2. Identifica los consumidores (quien lo importa).
3. Aplica la modificacion manteniendo:
   - Patron CommonJS (`require/module.exports`) en JS
   - ES modules (`import/export`) en TS
   - prop-types actualizados
   - classnames para clases condicionales
   - tabIndex para spatial navigation
4. Verifica que no rompe consumidores.

### Audit — Auditar Componente

Verifica:
- Tiene prop-types documentados?
- Tiene Less colocado?
- Usa classnames para clases condicionales?
- Soporta className externo?
- Tiene tabIndex para spatial nav?
- Usa i18n para strings?
- Tiene tests?
- Sigue el patron CommonJS correcto?

### Reglas
- **CommonJS** (`require/module.exports`) para archivos .js.
- **ES modules** (`import/export`) solo para archivos .ts/.tsx.
- **SIEMPRE** incluir prop-types.
- **SIEMPRE** soportar `className` prop.
- **SIEMPRE** tabIndex={0} en elementos interactivos.
- **NUNCA** CSS-in-JS, styled-components, o Tailwind.
- **NUNCA** estado de negocio en el componente (solo UI state).
- Colores de `stremio-colors`, no hardcodeados.
- 4 espacios de indentacion.
- Single quotes, semicolons.
