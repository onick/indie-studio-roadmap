# /stremio-audit — Auditoria Completa

Eres el **Stremio Web Expert**. Ejecuta una auditoria exhaustiva del codebase.

## Instrucciones

Analiza 8 dimensiones y genera score.

### 1. Architecture (WASM Bridge)
- Core service inicializa correctamente?
- useModelState se usa consistentemente?
- No hay estado de negocio fuera del core?
- CoreSuspender en todas las rutas que lo necesitan?
- Web Worker no bloqueado?
Score: [1-5]

### 2. Components (UI)
- Todos siguen el patron (folder, JS, Less, index)?
- prop-types documentados?
- className externo soportado?
- classnames para clases condicionales?
- No hay componentes duplicados?
Score: [1-5]

### 3. Routing
- Todos los regex en routesRegexp son validos?
- Rutas registradas en routes/index?
- Protected routes configuradas?
- Focus management en route transitions?
Score: [1-5]

### 4. Styling (Less)
- Usa stremio-colors (no colores hardcodeados)?
- Responsive con screen-sizes.less?
- No hay CSS-in-JS infiltrado?
- iOS safe areas manejadas?
Score: [1-5]

### 5. Performance
- partialCached donde aplica?
- Deep equality checks en hooks?
- No hay re-renders innecesarios?
- Lazy loading de imagenes?
- Bundle size razonable?
Score: [1-5]

### 6. Accessibility & Spatial Nav
- tabIndex en elementos interactivos?
- Focus visible styling?
- ARIA labels donde se necesita?
- Keyboard shortcuts documentados?
- Smart TV navigation funciona?
Score: [1-5]

### 7. i18n
- No hay strings hardcodeados?
- Todas las keys existen en traducciones?
- Scan de traducciones limpio?
- Interpolacion correcta?
Score: [1-5]

### 8. Testing & CI
- Tests existentes pasan?
- Coverage de componentes criticos?
- CI pipeline funcional?
- Linting sin errores?
- TypeScript sin errores?
Score: [1-5]

### Formato

```
================================================
  Stremio Web — Architecture Audit
  Date: YYYY-MM-DD | Version: X.Y.Z-beta.N
================================================

SCORE: [X/40]

Architecture:        [X/5]
Components:          [X/5]
Routing:             [X/5]
Styling:             [X/5]
Performance:         [X/5]
Accessibility:       [X/5]
i18n:                [X/5]
Testing & CI:        [X/5]

TOP 3 PROBLEMAS
1. [Problema → archivo → accion]
2. [Problema → archivo → accion]
3. [Problema → archivo → accion]

ACCIONES INMEDIATAS
- [ ] [Accion] → /stremio-[command]
```

### Reglas
- Score 35-40 = excelente, 25-34 = saludable, 18-24 = necesita atencion, <18 = emergencia.
- Se honesto — un audit suavizado no sirve.
