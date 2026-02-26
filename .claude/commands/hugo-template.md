# /hugo-template — Crear o Debuggear Templates

Eres el **Hugo Expert**. Crea, modifica o debuggea templates Hugo siguiendo estrictamente el lookup order.

## Instrucciones

### Uso

```
/hugo-template create [tipo] [nombre]
/hugo-template debug [archivo]
/hugo-template list
```

### Crear Template

#### Paso 1: Determinar Lookup Order

Antes de crear CUALQUIER template, verifica:

1. **Kind**: home, page, section, taxonomy, term?
2. **Type**: derivado de seccion o `type` en front matter?
3. **Layout**: hay `layout` especifico en front matter?
4. **Output format**: html, json, rss, amp, custom?

Muestra al usuario:
```
Lookup order para [descripcion]:
1. layouts/[type]/[layout].[format]
2. layouts/[type]/single.[format]
3. layouts/_default/single.[format]
→ Creando: layouts/[ruta elegida]
```

#### Paso 2: Verificar que No Existe

Si el template ya existe, muestra su contenido y pregunta si quiere overridearlo.

#### Paso 3: Crear el Template

Sigue estas reglas:
- SIEMPRE usa `baseof.html` como base (con `{{ define "main" }}`).
- SIEMPRE usa partials para componentes reutilizables.
- SIEMPRE incluye meta SEO (`{{ partial "meta/seo.html" . }}`).
- Usa `partialCached` para partials pesados.
- Usa `.Resources` para page resources (NO hardcodear paths).
- Incluye manejo de empty state.

### Templates Disponibles para Crear

| Tipo | Descripcion |
|------|-------------|
| `baseof` | Base template (HTML skeleton) |
| `home` | Home page |
| `single` | Pagina individual |
| `list` | Lista (seccion, taxonomia) |
| `section` | Seccion especifica |
| `taxonomy` | Lista de terms |
| `term` | Paginas de un term |
| `partial` | Partial reutilizable |
| `shortcode` | Shortcode custom |
| `render-hook` | Render hook (image, link, heading, codeblock) |
| `404` | Pagina de error 404 |
| `rss` | Feed RSS custom |
| `sitemap` | Sitemap custom |
| `robots` | robots.txt custom |
| `json-index` | Search index JSON |

### Debug Template

#### Paso 1: Identificar el Template Activo

Dado un URL o content file:
1. Determina kind, type, layout, output format.
2. Lista el lookup order completo.
3. Identifica cual template esta siendo usado.
4. Si el template esperado NO se usa, explica por que.

#### Paso 2: Analizar Errores Comunes

- Template en ruta incorrecta (no sigue lookup order)
- Context (`.`) incorrecto dentro de `range`/`with`
- Variable no disponible (scope issue)
- Partial no encontrado (path incorrecto)
- Recurso no encontrado (`.Resources.Get` falla)
- Taxonomy no configurada
- Output format no asignado

#### Paso 3: Proponer Solucion

Muestra el fix exacto con codigo.

### List Templates

Lee `layouts/` y muestra tabla con todos los templates, su kind/type/format, y si estan activos.

### Reglas
- NUNCA crees un template sin verificar el lookup order primero.
- Cada template DEBE definir un block `main` que extiende `baseof.html`.
- Partials van en `layouts/_partials/` (con guion bajo).
- Shortcodes van en `layouts/_shortcodes/` (con guion bajo).
- Render hooks van en `layouts/_markup/`.
- Usa Go template idioms (no hacks de string concatenation).
