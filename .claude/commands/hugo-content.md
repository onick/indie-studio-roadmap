# /hugo-content — Organizar Contenido

Eres el **Hugo Expert**. Organiza la estructura de contenido del sitio Hugo.

## Instrucciones

### Uso

```
/hugo-content structure          — Analiza y sugiere estructura
/hugo-content bundle [path]      — Convierte pagina a page bundle
/hugo-content taxonomy [name]    — Crea o configura taxonomia
/hugo-content archetype [name]   — Crea archetype para tipo de contenido
/hugo-content i18n [lang]        — Agrega idioma al sitio
/hugo-content migrate            — Migra contenido (flat → bundles, etc.)
```

### Structure — Analizar y Sugerir

1. Escanea `content/` completo.
2. Identifica:
   - Paginas sueltas vs bundles
   - Secciones con `_index.md` vs sin
   - Imagenes en `static/` que deberian estar en bundles
   - Front matter inconsistente (campos faltantes, formatos mixtos)
   - Taxonomias usadas pero no configuradas
3. Genera reporte:

```
================================================
  Content Structure Analysis
================================================

Sections: [N]
  blog/ — [N] pages ([M] bundles, [K] flat)
  docs/ — [N] pages

Issues:
  ⚠️ [N] pages with images in static/ (should be bundles)
  ⚠️ [N] sections without _index.md
  ⚠️ [N] pages missing description in front matter
  ⚠️ Mixed front matter formats (YAML: N, TOML: M)

Recommendations:
  1. [accion concreta]
  2. [accion concreta]
```

### Bundle — Convertir a Page Bundle

Dado `content/posts/my-post.md`:
1. Crea directorio `content/posts/my-post/`
2. Mueve el archivo a `content/posts/my-post/index.md`
3. Busca imagenes referenciadas en el contenido
4. Si estan en `static/`, mover al bundle
5. Actualiza referencias en el contenido
6. Verifica que el URL no cambio

### Taxonomy — Crear/Configurar

1. Agrega la taxonomia a `hugo.toml`
2. Crea archetype con el campo de la taxonomia
3. Crea `content/[taxonomy]/_index.md` (branch bundle)
4. Crea templates si no existen:
   - `layouts/taxonomy/list.html` (lista de terms)
   - `layouts/term/list.html` (paginas de un term)
5. Sugiere front matter para asignar terms al contenido existente

### Archetype — Crear

1. Determina la seccion y tipo de contenido
2. Crea `archetypes/[name].md` con:
   - Front matter completo con campos relevantes
   - Placeholder content con estructura del documento
   - Taxonomia fields vacios
3. Si es un bundle archetype, crea directorio:
   ```
   archetypes/[name]/
   ├── index.md
   └── placeholder.jpg
   ```

### i18n — Agregar Idioma

1. Agrega configuracion de idioma a `hugo.toml`
2. Decide: por filename o por directorio?
3. Crea `i18n/[lang].toml` con strings basicos
4. Crea `content/[lang]/` si es por directorio
5. Verifica que templates usan `i18n` y `relLangURL`
6. Crea language switcher partial si no existe

### Migrate — Migrar Contenido

**Flat a Bundles:**
Para cada pagina con imagenes asociadas:
1. Crea directorio
2. Mueve .md a index.md
3. Mueve imagenes al bundle
4. Actualiza referencias

**TOML a YAML front matter:**
1. Lee front matter actual
2. Convierte formato
3. Reescribe archivo

**Inconsistencias:**
1. Agrega campos faltantes (date, description, etc.)
2. Normaliza formatos de fecha
3. Asegura que taxonomias estan asignadas

### Reglas
- NUNCA borres contenido. Siempre mueve.
- Verifica URLs despues de cualquier restructuracion.
- Si hay aliases configurados, respétalos.
- Front matter YAML como default (el mas comun y legible).
- Cada seccion DEBE tener `_index.md`.
