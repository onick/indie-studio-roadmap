# Hugo Templating System — Reference

## Go Template Basics

Hugo usa `text/template` y `html/template` de Go. Los templates viven en `layouts/`.

### Actions

Todo entre `{{ }}` es una action. Whitespace control con hyphens: `{{- -}}`.

### Context (The Dot)

`.` es el contexto actual. En un single template, `.` es la Page. Dentro de `range` o `with`, `.` cambia. Usa `$` para el contexto raiz del template.

```html
{{ range .Pages }}
  {{ .Title }}          {{/* .Title de cada Page del range */}}
  {{ $.Site.Title }}    {{/* Accede al contexto raiz */}}
{{ end }}
```

### Variables

```html
{{ $var := "value" }}   {{/* Inicializacion */}}
{{ $var = "new" }}      {{/* Reasignacion */}}
```

Variables dentro de `if/range/with` son block-scoped.

### Funciones Esenciales

```html
{{/* Comparacion */}}
{{ eq $a $b }}    {{ ne $a $b }}
{{ gt $a $b }}    {{ ge $a $b }}
{{ lt $a $b }}    {{ le $a $b }}

{{/* Logica */}}
{{ and $a $b }}   {{ or $a $b }}   {{ not $a }}

{{/* Strings */}}
{{ lower .Title }}    {{ upper .Title }}
{{ truncate 100 .Summary }}
{{ replace .Content "old" "new" }}
{{ strings.TrimSuffix "/" .RelPermalink }}
{{ printf "%s — %s" .Title .Site.Title }}
{{ safeHTML .Params.raw_html }}

{{/* Collections */}}
{{ len .Pages }}
{{ first 5 .Pages }}
{{ last 3 .Pages }}
{{ after 2 .Pages }}
{{ where .Pages "Params.featured" true }}
{{ where .Pages "Section" "posts" }}
{{ sort .Pages "Date" "desc" }}
{{ shuffle .Pages }}
{{ union $a $b }}    {{ intersect $a $b }}

{{/* Math */}}
{{ add 1 2 }}    {{ sub 5 3 }}
{{ mul 2 3 }}    {{ div 10 2 }}
{{ mod 10 3 }}   {{ math.Round 3.7 }}

{{/* Dates */}}
{{ .Date.Format "2006-01-02" }}
{{ .Date.Format ":date_long" }}
{{ time.Format "January 2, 2006" .Date }}
{{ now }}

{{/* URLs */}}
{{ .Permalink }}       {{/* URL absoluta */}}
{{ .RelPermalink }}    {{/* URL relativa */}}
{{ relURL "css/main.css" }}
{{ absURL "images/logo.png" }}
{{ relLangURL "about/" }}  {{/* URL con prefijo de idioma */}}

{{/* Type checking */}}
{{ $type := printf "%T" .Params.value }}
{{ if reflect.IsSlice .Params.tags }}...{{ end }}

{{/* Data */}}
{{ .Site.Data.colors.primary }}   {{/* data/colors.toml -> primary */}}
{{ index .Params "my-key" }}      {{/* Keys con guiones */}}

{{/* Debugging */}}
{{ warnf "Value is: %v" $var }}
{{ debug.Dump . }}
```

## Partials

Ubicacion: `layouts/_partials/`.

```html
{{/* Incluir partial */}}
{{ partial "header.html" . }}

{{/* Partial con cache (mejora performance) */}}
{{ partialCached "sidebar.html" . .Section }}

{{/* Partial que retorna valor */}}
{{ $result := partial "calculate.html" (dict "a" 1 "b" 2) }}

{{/* Partial con dict context */}}
{{ partial "card.html" (dict "title" .Title "image" $img "page" .) }}
```

### Partial que retorna valor

```html
{{/* layouts/_partials/get-reading-time.html */}}
{{ $words := countwords .Content }}
{{ $minutes := div $words 200 }}
{{ return $minutes }}
```

## Base Templates y Blocks

```html
{{/* layouts/_default/baseof.html */}}
<!DOCTYPE html>
<html lang="{{ .Site.Language.Lang }}">
<head>
  {{ block "head" . }}
    <title>{{ .Title }} — {{ .Site.Title }}</title>
  {{ end }}
</head>
<body>
  {{ partial "header.html" . }}
  <main>
    {{ block "main" . }}{{ end }}
  </main>
  {{ partial "footer.html" . }}
  {{ block "scripts" . }}{{ end }}
</body>
</html>
```

```html
{{/* layouts/_default/single.html */}}
{{ define "main" }}
  <article>
    <h1>{{ .Title }}</h1>
    {{ .Content }}
  </article>
{{ end }}

{{ define "scripts" }}
  <script src="/js/prism.js"></script>
{{ end }}
```

## Lookup Order Completo

Hugo resuelve templates considerando (en orden de prioridad):

1. **Kind**: home, page, section, taxonomy, term
2. **Output format**: html, json, rss, amp
3. **Language**: .es.html, .fr.html
4. **Layout**: especificado en front matter
5. **Type**: derivado de front matter o seccion raiz
6. **Section**: nombre de la seccion

### Ejemplo para `content/blog/post.md`

```
1. layouts/blog/single.html.html
2. layouts/blog/single.html
3. layouts/_default/single.html.html
4. layouts/_default/single.html
```

### Ejemplo para home page

```
1. layouts/index.html.html
2. layouts/home.html.html
3. layouts/index.html
4. layouts/home.html
5. layouts/list.html.html
6. layouts/_default/index.html.html
7. layouts/_default/home.html.html
8. layouts/_default/index.html
9. layouts/_default/home.html
10. layouts/_default/list.html.html
11. layouts/_default/list.html
```

## Template Functions por Namespace

| Namespace | Funciones clave |
|-----------|----------------|
| `cast` | `ToFloat`, `ToInt`, `ToString` |
| `collections` | `where`, `first`, `last`, `sort`, `group`, `union`, `intersect`, `index`, `slice`, `dict` |
| `compare` | `eq`, `ne`, `gt`, `ge`, `lt`, `le`, `default`, `conditional` |
| `crypto` | `FNV32a`, `HMAC`, `MD5`, `SHA1`, `SHA256` |
| `css` | `Sass`, `PostCSS`, `TailwindCSS` |
| `encoding` | `Base64Decode`, `Base64Encode`, `Jsonify` |
| `fmt` | `Print`, `Printf`, `Println`, `Errorf`, `Warnf` |
| `hugo` | `BuildDate`, `CommitHash`, `Environment`, `Version` |
| `images` | `Brightness`, `ColorBalance`, `Gamma`, `GaussianBlur`, `Overlay`, `Text` |
| `inflect` | `Humanize`, `Pluralize`, `Singularize` |
| `js` | `Build` |
| `lang` | `FormatCurrency`, `FormatNumber`, `FormatPercent`, `Merge`, `Translate` |
| `math` | `Abs`, `Ceil`, `Floor`, `Log`, `Max`, `Min`, `Pow`, `Round`, `Sqrt` |
| `os` | `FileExists`, `Getenv`, `ReadDir`, `ReadFile`, `Stat` |
| `path` | `Base`, `BaseName`, `Clean`, `Dir`, `Ext`, `Join`, `Split` |
| `resources` | `ByType`, `Concat`, `Copy`, `Fingerprint`, `Get`, `GetMatch`, `GetRemote`, `Match`, `Minify` |
| `safe` | `CSS`, `HTML`, `HTMLAttr`, `JS`, `JSStr`, `URL` |
| `strings` | `Contains`, `Count`, `HasPrefix`, `HasSuffix`, `Repeat`, `Replace`, `ReplaceRE`, `Split`, `Title`, `ToLower`, `ToUpper`, `Trim`, `TrimLeft`, `TrimRight` |
| `time` | `AsTime`, `Duration`, `Format`, `Now`, `ParseDuration` |
| `transform` | `CanHighlight`, `Emojify`, `Highlight`, `HTMLEscape`, `HTMLUnescape`, `Markdownify`, `Plainify`, `Remarshal`, `Unmarshal` |
| `urls` | `AbsURL`, `AbsLangURL`, `Anchorize`, `JoinPath`, `Parse`, `Ref`, `RelLangURL`, `RelRef`, `RelURL` |

## Render Hooks

Templates especiales que interceptan el rendering de Markdown.

### Ubicacion

```
layouts/_markup/
├── render-image.html         # ![alt](src "title")
├── render-link.html          # [text](href "title")
├── render-heading.html       # ## Heading
├── render-codeblock.html     # ```lang ... ```
├── render-codeblock-mermaid.html  # ```mermaid ... ```
├── render-blockquote.html    # > blockquote
└── render-table.html         # | table |
```

### Ejemplo: Image Render Hook

```html
{{/* layouts/_markup/render-image.html */}}
{{ $img := .Page.Resources.GetMatch .Destination }}
{{ if $img }}
  {{ $webp := $img.Resize "800x webp q80" }}
  <figure>
    <img src="{{ $webp.RelPermalink }}"
         alt="{{ .Text }}"
         width="{{ $webp.Width }}"
         height="{{ $webp.Height }}"
         loading="lazy"
         decoding="async">
    {{ with .Title }}<figcaption>{{ . }}</figcaption>{{ end }}
  </figure>
{{ else }}
  <img src="{{ .Destination }}" alt="{{ .Text }}">
{{ end }}
```

### Ejemplo: Link Render Hook

```html
{{/* layouts/_markup/render-link.html */}}
<a href="{{ .Destination | safeURL }}"
  {{ if strings.HasPrefix .Destination "http" }}
    target="_blank" rel="noopener noreferrer"
  {{ end }}
  {{ with .Title }} title="{{ . }}"{{ end }}>
  {{ .Text }}
</a>
```

### Ejemplo: Codeblock Render Hook (Mermaid)

```html
{{/* layouts/_markup/render-codeblock-mermaid.html */}}
<div class="mermaid">
  {{ .Inner | safeHTML }}
</div>
{{ .Page.Store.Set "hasMermaid" true }}
```
