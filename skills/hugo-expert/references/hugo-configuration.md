# Hugo Configuration — Reference

## Formatos de Configuracion

Hugo soporta TOML, YAML y JSON. TOML es el default y mas comun.

```toml
# hugo.toml
baseURL = "https://example.com/"
languageCode = "en-us"
title = "My Site"
```

```yaml
# hugo.yaml
baseURL: https://example.com/
languageCode: en-us
title: My Site
```

## Config Directory

Para proyectos complejos, divide la config en multiples archivos:

```
config/
├── _default/          # Base config (siempre se carga)
│   ├── hugo.toml      # Core settings
│   ├── menus.toml     # Menu configuration
│   ├── params.toml    # Site parameters
│   ├── languages.toml # Language settings
│   └── module.toml    # Module imports
├── production/        # Override para production
│   ├── hugo.toml      # baseURL, minify, etc.
│   └── params.toml
├── staging/
│   └── hugo.toml
└── development/
    └── hugo.toml
```

```bash
# Build con ambiente especifico
hugo --environment production
hugo server --environment staging
```

El ambiente default es `production` para `hugo` y `development` para `hugo server`.

## Configuracion Completa

```toml
# hugo.toml — Todas las opciones principales

# === Core ===
baseURL = "https://example.com/"
title = "My Hugo Site"
languageCode = "en-us"
theme = "my-theme"                      # O usar modules
copyright = "© 2025 My Company"
paginate = 10                           # Items por pagina en listas
paginatePath = "page"                   # /blog/page/2/
timeZone = "America/New_York"           # IANA timezone
enableRobotsTXT = true                  # Generar robots.txt
enableGitInfo = false                   # .GitInfo en paginas
enableEmoji = true                      # Soporte de emoji :emoji:
enableInlineShortcodes = false          # Seguridad: off por default
disableKinds = []                       # Desactivar: "page", "section", "taxonomy", "term", "RSS", "sitemap", "robotsTXT", "404"
removePathAccents = true                # café → cafe en URLs
hasCJKLanguage = false                  # CJK word counting
summaryLength = 70                      # Palabras en .Summary
disableHugoGeneratorInject = false      # Quitar meta generator

# === Build ===
[build]
  [build.buildStats]
    enable = false                      # Stats para PurgeCSS
  [build.cachebusters]
    # Invalidar cache cuando cambian estos
    [[build.cachebusters.match]]
      source = "assets/.*\\.(.*)$"
      target = "(.*)$"

# === Build Flags ===
buildDrafts = false                     # Incluir drafts
buildFuture = false                     # Incluir publishDate futuro
buildExpired = false                    # Incluir expiryDate pasado

# === Markup ===
[markup]
  defaultMarkdownHandler = "goldmark"
  [markup.goldmark]
    [markup.goldmark.extensions]
      definitionList = true
      footnote = true
      linkify = true
      linkifyProtocol = "https"
      strikethrough = true
      table = true
      taskList = true
      typographer = true
    [markup.goldmark.extensions.cjk]
      enable = false
    [markup.goldmark.extensions.passthrough]
      enable = false                    # LaTeX/KaTeX passthrough
      [markup.goldmark.extensions.passthrough.delimiters]
        block = [["\\[", "\\]"], ["$$", "$$"]]
        inline = [["\\(", "\\)"], ["$", "$"]]
    [markup.goldmark.parser]
      autoHeadingID = true
      autoHeadingIDType = "github"      # "github", "github-ascii", "blackfriday"
      [markup.goldmark.parser.attribute]
        block = false                   # {.class #id} en blocks
        title = true
    [markup.goldmark.renderer]
      hardWraps = false                 # Newlines como <br>
      unsafe = false                    # HTML en Markdown

  [markup.highlight]
    codeFences = true
    guessSyntax = false
    lineNos = false
    lineNumbersInTable = true
    noClasses = true                    # Inline styles vs CSS classes
    style = "monokai"                   # Chroma style
    tabWidth = 4

  [markup.tableOfContents]
    startLevel = 2
    endLevel = 3
    ordered = false

# === Outputs ===
[outputs]
  home = ["html", "rss"]
  section = ["html", "rss"]
  taxonomy = ["html"]
  term = ["html", "rss"]

# === Output Formats ===
[outputFormats]
  [outputFormats.SearchIndex]
    baseName = "search-index"
    mediaType = "application/json"
    isPlainText = true
    notAlternative = true

# === Media Types ===
[mediaTypes]
  [mediaTypes."application/json"]
    suffixes = ["json"]

# === Taxonomies ===
[taxonomies]
  tag = "tags"
  category = "categories"
  series = "series"

# === Permalinks ===
[permalinks]
  posts = "/:year/:month/:slug/"
  pages = "/:slug/"

# === Related Content ===
[related]
  includeNewer = true
  threshold = 80
  toLower = true
  [[related.indices]]
    name = "tags"
    weight = 100
  [[related.indices]]
    name = "categories"
    weight = 80
  [[related.indices]]
    name = "date"
    weight = 10
    type = "datetime"

# === Sitemap ===
[sitemap]
  changeFreq = ""
  disable = false
  filename = "sitemap.xml"
  priority = -1

# === Minify ===
[minify]
  disableCSS = false
  disableHTML = false
  disableJS = false
  disableJSON = false
  disableSVG = false
  disableXML = false
  minifyOutput = false                  # Minificar HTML output
  [minify.tdewolff]
    [minify.tdewolff.html]
      keepWhitespace = false

# === Security ===
[security]
  enableInlineShortcodes = false
  [security.exec]
    allow = ["^(dart-)?sass(-embedded)?$", "^go$", "^npx$", "^postcss$", "^tailwindcss$"]
    osEnv = ["(?i)^(PATH|PATHEXT|APPDATA|TMP|TEMP|TERM|GO\\w+|NP\\w+|HOME)$"]
  [security.funcs]
    getenv = ["^HUGO_", "^CI$"]
  [security.http]
    methods = ["(?i)GET|POST"]
    urls = [".*"]

# === Server (dev only) ===
[server]
  [[server.headers]]
    for = "/**"
    [server.headers.values]
      X-Frame-Options = "DENY"
      Content-Security-Policy = "default-src 'self'"
  [[server.redirects]]
    from = "/old-url/**"
    to = "/new-url/:splat"
    status = 301

# === Caches ===
[caches]
  [caches.assets]
    dir = ":resourceDir/_gen"
    maxAge = -1                         # No expira
  [caches.getcsv]
    dir = ":cacheDir/:project"
    maxAge = "10m"
  [caches.getjson]
    dir = ":cacheDir/:project"
    maxAge = "10m"
  [caches.getresource]
    dir = ":cacheDir/:project"
    maxAge = "24h"
  [caches.images]
    dir = ":resourceDir/_gen"
    maxAge = -1
  [caches.modules]
    dir = ":cacheDir/modules"
    maxAge = -1

# === Params (custom) ===
[params]
  description = "My awesome Hugo site"
  author = "John Doe"
  mainSections = ["posts", "articles"]
  showToc = true
  dateFormat = "January 2, 2006"
  [params.social]
    twitter = "johndoe"
    github = "johndoe"
  [params.analytics]
    googleAnalytics = "G-XXXXXXXXXX"
```

## Variables de Entorno

```bash
# Override cualquier config
HUGO_BASEURL="https://staging.example.com/"
HUGO_PARAMS_DESCRIPTION="Staging site"
HUGO_ENVIRONMENT="staging"

# Cache
HUGO_CACHEDIR="/tmp/hugo-cache"

# Modules
HUGO_MODULE_WORKSPACE=".work"
HUGO_MODULE_VENDOR=true

# Build
HUGO_NUMWORKERMULTIPLIER=2
HUGO_ENABLEGITINFO=true
```

Convention: `HUGO_` + config key en UPPER_SNAKE_CASE. Nested: usa `_` (ej: `HUGO_PARAMS_SOCIAL_TWITTER`).

## Custom Output Formats

```toml
# Definir formato
[outputFormats.SearchIndex]
  baseName = "search-index"
  mediaType = "application/json"
  isPlainText = true
  notAlternative = true

# Asignar a pagina
[outputs]
  home = ["html", "rss", "SearchIndex"]
```

```html
{{/* layouts/index.searchindex.json */}}
[
  {{ range $i, $page := .Site.RegularPages }}
  {{ if $i }},{{ end }}
  {
    "title": {{ .Title | jsonify }},
    "url": {{ .RelPermalink | jsonify }},
    "content": {{ .Plain | jsonify }},
    "tags": {{ .Params.tags | jsonify }}
  }
  {{ end }}
]
```

## Merge Strategy

Cuando usas config directory o modulos, Hugo merge configs:

```toml
# _merge options: none, shallow, deep
[params]
  _merge = "deep"       # Deep merge params de tema y proyecto
[menus]
  _merge = "shallow"    # Solo primer nivel
[languages]
  _merge = "none"       # No merge, solo el del proyecto
```
