# Hugo Content Management — Reference

## Page Bundles

### Leaf Bundle (Pagina individual + recursos)

```
content/posts/my-post/
├── index.md          # La pagina (OBLIGATORIO para leaf bundle)
├── hero.jpg          # Page resource
├── diagram.svg       # Page resource
├── data.csv          # Page resource
└── gallery/
    ├── photo-1.jpg   # Page resource (subdirectory)
    └── photo-2.jpg
```

- Usa `index.md` (sin guion bajo)
- NO puede tener paginas hijas
- Todos los archivos son page resources accesibles via `.Resources`
- Otros .md en el bundle son resources, NO paginas separadas

### Branch Bundle (Seccion + paginas hijas)

```
content/blog/
├── _index.md         # Pagina de la seccion (OBLIGATORIO para branch)
├── featured.jpg      # Page resource del branch
├── post-1.md         # Pagina hija (NO resource)
├── post-2/
│   ├── index.md      # Leaf bundle hijo
│   └── image.png
└── tutorials/
    ├── _index.md     # Sub-seccion (branch anidado)
    └── tutorial-1.md
```

- Usa `_index.md` (CON guion bajo)
- Puede tener descendientes (leaf bundles, otros branches, paginas simples)
- Los .md hijos son paginas, NO resources
- Solo archivos no-.md son resources del branch

### Headless Bundles

```yaml
# En front matter de index.md
build:
  render: never
  list: never
```

No se publican como paginas pero sus recursos son accesibles desde otros templates.

## Taxonomias

### Default: tags y categories

```toml
# hugo.toml (default, no necesitas declararlo)
[taxonomies]
  tag = "tags"
  category = "categories"
```

### Custom Taxonomies

```toml
[taxonomies]
  tag = "tags"
  category = "categories"
  series = "series"
  author = "authors"
  technology = "technologies"
```

### Asignar en Front Matter

```yaml
---
title: "Mi Post"
tags: ["hugo", "go", "static-site"]
categories: ["tutorials"]
series: ["hugo-from-scratch"]
authors: ["juan", "maria"]
technologies: ["hugo", "tailwindcss"]
---
```

### Taxonomy Weight

```yaml
---
tags: ["hugo"]
tags_weight: 10    # Peso de esta pagina en la taxonomia "tags"
---
```

### Templates de Taxonomias

```
layouts/
├── taxonomy/
│   └── list.html       # Lista de TODOS los terms (ej: /tags/)
├── term/
│   └── list.html       # Paginas de UN term (ej: /tags/hugo/)
├── tags/
│   ├── taxonomy.html   # Override especifico para tags
│   └── term.html       # Override especifico para tag individual
```

### Acceder a Taxonomias en Templates

```html
{{/* Lista de terms de una taxonomia */}}
{{ range .Site.Taxonomies.tags }}
  <a href="{{ .Page.RelPermalink }}">{{ .Page.Title }} ({{ .Count }})</a>
{{ end }}

{{/* Tags de la pagina actual */}}
{{ range .GetTerms "tags" }}
  <a href="{{ .RelPermalink }}">{{ .LinkTitle }}</a>
{{ end }}

{{/* Paginas con un tag especifico */}}
{{ range .Site.Taxonomies.tags.hugo }}
  <a href="{{ .Page.RelPermalink }}">{{ .Page.Title }}</a>
{{ end }}
```

## Archetypes

Ubicacion: `archetypes/`

### Default

```yaml
# archetypes/default.md
---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
draft: true
---
```

### Por seccion

```yaml
# archetypes/posts.md
---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
draft: true
tags: []
categories: []
description: ""
params:
  featured: false
  author: ""
---

## Introduccion

## Contenido

## Conclusion
```

### Bundle Archetype

```
archetypes/posts/
├── index.md          # Template del contenido
└── hero.jpg          # Imagen placeholder
```

### Crear contenido

```bash
hugo new content posts/my-new-post.md           # Usa archetypes/posts.md
hugo new content posts/my-bundle-post/index.md   # Usa archetypes/posts/
hugo new content tutorials/first-tutorial.md      # Usa archetypes/tutorials.md o default.md
```

## Front Matter Completo

### Campos Predefinidos

| Campo | Tipo | Descripcion |
|-------|------|-------------|
| `title` | string | Titulo de la pagina |
| `date` | datetime | Fecha de creacion |
| `lastmod` | datetime | Ultima modificacion |
| `publishDate` | datetime | No publicar antes de esta fecha |
| `expiryDate` | datetime | No publicar despues de esta fecha |
| `draft` | bool | Si es borrador |
| `weight` | int | Orden en colecciones |
| `slug` | string | Override del segmento final de URL |
| `url` | string | Override de URL completa |
| `aliases` | []string | URLs que redirigen a esta pagina |
| `type` | string | Content type (override de seccion) |
| `layout` | string | Template layout (override de lookup) |
| `description` | string | Meta description |
| `summary` | string | Resumen custom |
| `keywords` | []string | Meta keywords |
| `linkTitle` | string | Titulo corto para links/menus |
| `outputs` | []string | Output formats a generar |
| `cascade` | map | Front matter heredado a descendientes |
| `markup` | string | Formato de contenido (md, html, org) |
| `headless` | bool | Bundle sin publicar (legacy) |
| `translationKey` | string | Key para enlazar traducciones |

### Cascade con Target

```yaml
---
title: "Blog"
cascade:
  - params:
      show_sidebar: true
    _target:
      kind: page
      path: "/blog/**"
  - params:
      show_sidebar: false
    _target:
      kind: section
---
```

## Multilingual

### Por filename

```
content/
├── about.en.md
├── about.es.md
├── posts/
│   ├── my-post.en.md
│   └── my-post.es.md
```

### Por directorio

```
content/
├── en/
│   ├── about.md
│   └── posts/
│       └── my-post.md
├── es/
│   ├── about.md
│   └── posts/
│       └── mi-post.md
```

### Translation Key

```yaml
# content/en/about.md
---
translationKey: "about"
---
```

```yaml
# content/es/acerca-de.md
---
translationKey: "about"
---
```

### Strings i18n

```toml
# i18n/en.toml
[readMore]
other = "Read more"

[postsCount]
one = "One post"
other = "{{ .Count }} posts"
```

```toml
# i18n/es.toml
[readMore]
other = "Leer más"

[postsCount]
one = "Un artículo"
other = "{{ .Count }} artículos"
```

```html
{{/* En templates */}}
{{ i18n "readMore" }}
{{ i18n "postsCount" (dict "Count" (len .Pages)) }}
```

### Language Switcher

```html
{{ if .IsTranslated }}
  {{ range .Translations }}
    <a href="{{ .RelPermalink }}" hreflang="{{ .Lang }}">
      {{ .Language.LanguageName }}
    </a>
  {{ end }}
{{ end }}
```

## Page Resources

### Acceder a recursos de un page bundle

```html
{{/* Un recurso especifico */}}
{{ $img := .Resources.GetMatch "hero.*" }}

{{/* Todos los JPG */}}
{{ $photos := .Resources.Match "gallery/*.jpg" }}

{{/* Por tipo */}}
{{ $images := .Resources.ByType "image" }}

{{/* Iterar */}}
{{ range .Resources.Match "documents/*.pdf" }}
  <a href="{{ .RelPermalink }}">{{ .Name }}</a>
{{ end }}
```

### Resource Metadata (en front matter)

```yaml
---
resources:
  - src: "hero.jpg"
    title: "Hero Image"
    params:
      alt: "Landscape photo"
      credits: "Photo by Juan"
  - src: "documents/*.pdf"
    params:
      icon: "pdf"
---
```

## Page Collections

### Metodos principales

```html
{{ .Pages }}              {{/* Paginas hijas directas */}}
{{ .RegularPages }}       {{/* Solo paginas regulares (no secciones) */}}
{{ .Sections }}           {{/* Solo sub-secciones */}}
{{ .Site.Pages }}         {{/* TODAS las paginas del sitio */}}
{{ .Site.RegularPages }}  {{/* TODAS las paginas regulares */}}

{{/* Filtrar */}}
{{ where .Site.RegularPages "Section" "blog" }}
{{ where .Pages "Params.featured" true }}
{{ where .Pages "Date" ">" (now.AddDate -1 0 0) }}

{{/* Ordenar */}}
{{ range .Pages.ByTitle }}...{{ end }}
{{ range .Pages.ByDate }}...{{ end }}
{{ range .Pages.ByDate.Reverse }}...{{ end }}
{{ range .Pages.ByWeight }}...{{ end }}
{{ range .Pages.ByParam "author" }}...{{ end }}

{{/* Agrupar */}}
{{ range .Pages.GroupByDate "2006-01" }}
  <h2>{{ .Key }}</h2>
  {{ range .Pages }}...{{ end }}
{{ end }}

{{ range .Pages.GroupByParam "author" }}
  <h2>{{ .Key }}</h2>
  {{ range .Pages }}...{{ end }}
{{ end }}
```

## Menus

### Configuracion

```toml
# hugo.toml
[[menus.main]]
  name = "Home"
  pageRef = "/"
  weight = 10

[[menus.main]]
  name = "Blog"
  pageRef = "/blog"
  weight = 20

[[menus.main]]
  name = "About"
  pageRef = "/about"
  weight = 30

[[menus.main]]
  name = "GitHub"
  url = "https://github.com"
  weight = 40
  [menus.main.params]
    external = true
```

### En front matter

```yaml
---
menus:
  main:
    name: "Mi Post"
    weight: 25
    parent: "Blog"
---
```

### Render menu

```html
<nav>
  <ul>
    {{ range .Site.Menus.main }}
      <li class="{{ if .HasChildren }}has-children{{ end }}">
        <a href="{{ .URL }}"
           {{ if $.IsMenuCurrent "main" . }}class="active"{{ end }}
           {{ with .Params.external }}target="_blank" rel="noopener"{{ end }}>
          {{ .Name }}
        </a>
        {{ if .HasChildren }}
          <ul>
            {{ range .Children }}
              <li><a href="{{ .URL }}">{{ .Name }}</a></li>
            {{ end }}
          </ul>
        {{ end }}
      </li>
    {{ end }}
  </ul>
</nav>
```
