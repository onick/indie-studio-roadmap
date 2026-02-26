# Hugo Pipes & Asset Pipeline — Reference

## Conceptos Base

Hugo Pipes procesa archivos en `assets/` (NO en `static/`). Los archivos en `static/` se copian sin procesar.

```html
{{/* Obtener recurso global */}}
{{ $css := resources.Get "css/main.css" }}

{{/* Obtener recurso remoto */}}
{{ $data := resources.GetRemote "https://api.example.com/data.json" }}

{{/* Publicar (genera archivo en public/) */}}
{{ $css.Permalink }}      {{/* URL absoluta */}}
{{ $css.RelPermalink }}   {{/* URL relativa */}}
{{ $css.Content }}        {{/* Contenido inline */}}
```

## SCSS / Sass

### Transpiling basico

```html
{{ $opts := dict "transpiler" "dartsass" }}
{{ $styles := resources.Get "css/main.scss" | css.Sass $opts }}
<link rel="stylesheet" href="{{ $styles.RelPermalink }}">
```

### Opciones de css.Sass

```html
{{ $opts := dict
  "transpiler" "dartsass"        {{/* "dartsass" (recomendado) o "libsass" */}}
  "targetPath" "css/style.css"   {{/* Ruta de output */}}
  "outputStyle" "compressed"     {{/* "nested", "expanded", "compact", "compressed" */}}
  "enableSourceMap" true         {{/* Source maps */}}
  "includePaths" (slice "node_modules") {{/* Rutas adicionales de include */}}
}}
{{ $styles := resources.Get "css/main.scss" | css.Sass $opts }}
```

### Estructura SCSS tipica

```
assets/
└── css/
    ├── main.scss        # Entry point
    ├── _variables.scss  # Variables
    ├── _mixins.scss     # Mixins
    ├── _base.scss       # Reset/base styles
    ├── _layout.scss     # Layout
    └── components/
        ├── _header.scss
        ├── _footer.scss
        └── _card.scss
```

## Tailwind CSS

### Configuracion (Hugo v0.128+)

```html
{{/* layouts/_partials/css.html */}}
{{ with resources.Get "css/main.css" }}
  {{ $opts := dict "minify" true }}
  {{ with . | css.TailwindCSS $opts }}
    {{ if hugo.IsDevelopment }}
      <link rel="stylesheet" href="{{ .RelPermalink }}">
    {{ else }}
      <link rel="stylesheet" href="{{ (.RelPermalink) }}" integrity="{{ (.Data.Integrity) }}">
    {{ end }}
  {{ end }}
{{ end }}
```

### Archivo CSS entry point

```css
/* assets/css/main.css */
@import "tailwindcss";
```

### Prerequisitos

```bash
# Instalar Tailwind CSS CLI como standalone o via npm
npm install -D @tailwindcss/cli
# O instalar el binario standalone de Tailwind
```

## PostCSS

### Configuracion

```html
{{ $css := resources.Get "css/main.css" | css.PostCSS | resources.Minify }}
```

### postcss.config.js

```js
module.exports = {
  plugins: {
    'postcss-import': {},
    'postcss-nesting': {},
    autoprefixer: {},
  }
};
```

### Opciones de css.PostCSS

```html
{{ $opts := dict
  "config" "path/to/postcss.config.js"  {{/* Config file */}}
  "noMap" true                           {{/* Disable source maps */}}
  "inlineImports" true                   {{/* Inline @import */}}
}}
{{ $css := resources.Get "css/main.css" | css.PostCSS $opts }}
```

## JavaScript con ESBuild

### Build basico

```html
{{ $js := resources.Get "js/main.ts" | js.Build }}
<script src="{{ $js.RelPermalink }}"></script>
```

### Opciones completas

```html
{{ $opts := dict
  "targetPath" "js/app.js"          {{/* Output path */}}
  "minify" true                      {{/* Minificar */}}
  "target" "es2020"                  {{/* Target: es2015-es2024, esnext */}}
  "format" "esm"                     {{/* "iife", "cjs", "esm" */}}
  "sourceMap" "linked"               {{/* "inline", "linked", "external" */}}
  "externals" (slice "react" "vue")  {{/* No bundlear estos */}}
  "defines" (dict
    "process.env.NODE_ENV" "'production'"
  )
  "shims" (dict
    "react" "preact/compat"
  )
  "inject" (slice "js/shims/process.js")
  "params" (dict
    "apiUrl" .Site.Params.apiUrl
    "version" .Site.Params.version
  )
}}
{{ $js := resources.Get "js/main.ts" | js.Build $opts }}
```

### Acceder a params desde JS

```typescript
// assets/js/main.ts
import * as params from '@params';

console.log(params.apiUrl);  // Valor pasado desde template
```

### Import desde assets/

```typescript
// assets/js/main.ts
import { formatDate } from './utils/date';  // assets/js/utils/date.ts
import data from '../data/config.json';      // assets/data/config.json
```

### TypeScript + JSX

```html
{{ $opts := dict
  "target" "es2020"
  "format" "esm"
  "minify" true
  "jsx" "automatic"
  "jsxImportSource" "preact"
}}
{{ $js := resources.Get "js/app.tsx" | js.Build $opts }}
```

## Image Processing

### Metodos principales

```html
{{ $img := .Resources.GetMatch "hero.*" }}

{{/* Resize — cambiar dimensiones */}}
{{ $resized := $img.Resize "800x" }}         {{/* Ancho 800px, alto auto */}}
{{ $resized := $img.Resize "x400" }}         {{/* Alto 400px, ancho auto */}}
{{ $resized := $img.Resize "800x400" }}      {{/* Exacto 800x400 */}}

{{/* Fit — encajar dentro de dimensiones (no crop) */}}
{{ $fit := $img.Fit "800x400" }}

{{/* Fill — rellenar dimensiones (crop automatico) */}}
{{ $fill := $img.Fill "800x400 center" }}    {{/* Anclas: center, smart, top, bottom, left, right */}}

{{/* Crop — recortar */}}
{{ $crop := $img.Crop "400x400 smart" }}

{{/* Convertir formato */}}
{{ $webp := $img.Resize "800x webp" }}
{{ $avif := $img.Resize "800x avif" }}       {{/* Hugo 0.131+ */}}

{{/* Calidad */}}
{{ $jpg := $img.Resize "800x q75" }}         {{/* 1-100, default 75 */}}
{{ $webp := $img.Resize "800x webp q80" }}
```

### Filtros de imagen

```html
{{ $img := .Resources.GetMatch "photo.jpg" }}

{{ $blurred := $img | images.Filter (images.GaussianBlur 8) }}
{{ $bright := $img | images.Filter (images.Brightness 20) }}
{{ $contrast := $img | images.Filter (images.Contrast 30) }}
{{ $gray := $img | images.Filter (images.Grayscale) }}
{{ $sepia := $img | images.Filter (images.Sepia 80) }}
{{ $saturated := $img | images.Filter (images.Saturation 50) }}

{{/* Multiple filtros */}}
{{ $processed := $img | images.Filter
  (images.GaussianBlur 3)
  (images.Brightness 10)
  (images.Contrast 15)
}}

{{/* Overlay (watermark) */}}
{{ $watermark := resources.Get "images/watermark.png" }}
{{ $result := $img | images.Filter (images.Overlay $watermark 20 20) }}

{{/* Text overlay */}}
{{ $result := $img | images.Filter (images.Text "Copyright 2025"
  (dict "size" 24 "color" "#ffffff" "x" 10 "y" 10)
) }}
```

### EXIF Data

```html
{{ with $img.Exif }}
  Camera: {{ .Tags.Make }} {{ .Tags.Model }}
  Date: {{ .Date }}
  GPS: {{ .Lat }}, {{ .Long }}
{{ end }}
```

### Patron: Responsive Images

```html
{{ $img := .Resources.GetMatch "hero.*" }}
{{ $sizes := slice "480" "768" "1024" "1440" }}
{{ $srcset := slice }}
{{ range $sizes }}
  {{ $resized := $img.Resize (printf "%sx webp" .) }}
  {{ $srcset = $srcset | append (printf "%s %sw" $resized.RelPermalink .) }}
{{ end }}
<img
  src="{{ ($img.Resize "800x webp").RelPermalink }}"
  srcset="{{ delimit $srcset ", " }}"
  sizes="(max-width: 480px) 480px, (max-width: 768px) 768px, 1024px"
  alt="{{ .Title }}"
  width="{{ $img.Width }}"
  height="{{ $img.Height }}"
  loading="lazy"
  decoding="async"
>
```

## Fingerprinting & SRI

```html
{{ $css := resources.Get "css/main.css" | resources.Minify | resources.Fingerprint "sha384" }}
<link rel="stylesheet" href="{{ $css.RelPermalink }}" integrity="{{ $css.Data.Integrity }}" crossorigin="anonymous">

{{ $js := resources.Get "js/app.js" | js.Build | resources.Minify | resources.Fingerprint "sha384" }}
<script src="{{ $js.RelPermalink }}" integrity="{{ $js.Data.Integrity }}" crossorigin="anonymous"></script>
```

Algoritmos: `md5`, `sha256`, `sha384`, `sha512`. Default: `sha256`.

## Minificacion

```html
{{/* Individual */}}
{{ $css := resources.Get "css/main.css" | resources.Minify }}
{{ $js := resources.Get "js/app.js" | resources.Minify }}
{{ $html := resources.Get "partials/embed.html" | resources.Minify }}

{{/* Global (en CLI) */}}
hugo --minify
```

## Concatenacion

```html
{{ $normalize := resources.Get "css/normalize.css" }}
{{ $custom := resources.Get "css/custom.css" }}
{{ $combined := slice $normalize $custom | resources.Concat "css/bundle.css" }}
```

## Remote Resources

```html
{{ $data := resources.GetRemote "https://api.github.com/repos/gohugoio/hugo" }}
{{ if $data.Err }}
  {{ warnf "Failed to fetch: %s" $data.Err }}
{{ else }}
  {{ $json := $data.Content | transform.Unmarshal }}
  Stars: {{ $json.stargazers_count }}
{{ end }}
```

### Con opciones

```html
{{ $opts := dict
  "headers" (dict "Authorization" "Bearer TOKEN")
}}
{{ $data := resources.GetRemote "https://api.example.com/data" $opts }}
```

## Pipeline Completo (Ejemplo Produccion)

```html
{{/* layouts/_partials/head/styles.html */}}
{{ $opts := dict "transpiler" "dartsass" "targetPath" "css/main.css" }}
{{ $styles := resources.Get "css/main.scss" | css.Sass $opts }}
{{ if hugo.IsProduction }}
  {{ $styles = $styles | resources.PostCSS | resources.Minify | resources.Fingerprint "sha384" }}
  <link rel="stylesheet" href="{{ $styles.RelPermalink }}" integrity="{{ $styles.Data.Integrity }}" crossorigin="anonymous">
{{ else }}
  <link rel="stylesheet" href="{{ $styles.RelPermalink }}">
{{ end }}
```

```html
{{/* layouts/_partials/head/scripts.html */}}
{{ $opts := dict "target" "es2020" "format" "esm" "params" (dict "env" hugo.Environment) }}
{{ $js := resources.Get "js/main.ts" | js.Build $opts }}
{{ if hugo.IsProduction }}
  {{ $js = $js | resources.Minify | resources.Fingerprint "sha384" }}
  <script type="module" src="{{ $js.RelPermalink }}" integrity="{{ $js.Data.Integrity }}" crossorigin="anonymous"></script>
{{ else }}
  <script type="module" src="{{ $js.RelPermalink }}"></script>
{{ end }}
```
