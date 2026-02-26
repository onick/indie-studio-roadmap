# /hugo-audit — Auditoria Completa del Sitio

Eres el **Hugo Expert**. Ejecuta una auditoria exhaustiva del sitio Hugo.

## Instrucciones

Analiza estas 8 dimensiones y genera un reporte con score por dimension.

### 1. Content Architecture (Estructura de Contenido)

- Secciones bien definidas con `_index.md`?
- Page bundles para contenido con recursos?
- No hay imagenes huerfanas en `static/`?
- Taxonomias configuradas y usadas?
- Archetypes para todos los tipos de contenido?
- Front matter consistente?
Score: [1-5]

### 2. Template Quality (Calidad de Templates)

- Templates siguen el lookup order correcto?
- Usan `baseof.html` + blocks?
- Partials son reutilizables y con cache?
- No hay logica duplicada entre templates?
- Render hooks para Markdown?
- Templates de error (404)?
Score: [1-5]

### 3. Asset Pipeline (Pipeline de Assets)

- Usa Hugo Pipes (no herramientas externas)?
- CSS minificado + fingerprinted?
- JS bundled con ESBuild?
- Imagenes procesadas (WebP, responsive)?
- Source maps solo en development?
- SRI (Subresource Integrity)?
Score: [1-5]

### 4. Performance (Rendimiento)

- Build time razonable?
- `partialCached` en partials pesados?
- Imagenes lazy loaded con width/height?
- CSS critico inline?
- JS defer/module?
- No hay assets bloqueantes?
Score: [1-5]

### 5. SEO & Accessibility

- Meta tags completos (title, description)?
- Open Graph y Twitter Cards?
- JSON-LD structured data?
- Sitemap generado?
- robots.txt correcto?
- Canonical URLs?
- Hreflang (si multilingue)?
- Alt text en imagenes?
- Semantic HTML?
Score: [1-5]

### 6. Configuration (Configuracion)

- `hugo.toml` organizado (o config directory)?
- Permalinks configurados?
- Related content configurado?
- Security settings correctos?
- Environments (dev vs prod)?
- Markup options apropiados?
Score: [1-5]

### 7. Multilingual (si aplica)

- Idiomas configurados correctamente?
- Strings i18n para UI?
- Language switcher funcional?
- Contenido traducido o con translation keys?
- URLs con prefijo de idioma?
Score: [1-5] o N/A

### 8. Deployment & CI/CD

- Build automatizado?
- Hugo version pinned?
- Deploy a staging posible?
- Headers de seguridad?
- Cache headers para assets?
- SSL/HTTPS?
Score: [1-5]

### Formato del Reporte

```
================================================
  Hugo Site — Architecture Audit
  Date: YYYY-MM-DD
================================================

SCORE GENERAL: [X/40] (o /35 si no multilingue)

Content Architecture:  [X/5] ████░
Template Quality:      [X/5] ███░░
Asset Pipeline:        [X/5] █████
Performance:           [X/5] ████░
SEO & Accessibility:   [X/5] ███░░
Configuration:         [X/5] ████░
Multilingual:          [X/5] ██░░░  (o N/A)
Deployment & CI/CD:    [X/5] ███░░

TOP 3 PROBLEMAS CRITICOS
1. [Problema + archivo + accion]
2. [Problema + archivo + accion]
3. [Problema + archivo + accion]

TOP 3 FORTALEZAS
1. [Que funciona bien]
2. [Que funciona bien]
3. [Que funciona bien]

ACCIONES INMEDIATAS
- [ ] [Accion 1] → Comando: /hugo-[xxx]
- [ ] [Accion 2] → Comando: /hugo-[xxx]
- [ ] [Accion 3] → Comando: /hugo-[xxx]
```

### Reglas
- Scores: 35-40 excelente, 25-34 saludable, 18-24 necesita atencion, <18 emergencia.
- Cada accion inmediata debe referir al comando relevante.
- Se honesto. Un audit suavizado no sirve.
- Lee TODOS los templates, no solo los principales.
