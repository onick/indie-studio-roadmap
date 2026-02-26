# Hugo Performance & SEO — Reference

## Build Performance

### Factores de velocidad

Hugo es extremadamente rapido (~1ms/pagina). Los cuellos de botella:

1. **Image processing** — Resize/filter de imagenes es la operacion mas lenta
2. **Remote resources** — `resources.GetRemote` hace HTTP requests
3. **Sass/PostCSS/Tailwind** — Compilacion de CSS
4. **Large sites** — 10K+ paginas requieren optimizacion

### Optimizaciones de Build

```toml
# hugo.toml

# Paralelismo (default: NumCPU)
# HUGO_NUMWORKERMULTIPLIER = 2

# Cache agresivo
[caches]
  [caches.assets]
    maxAge = -1        # Nunca expira
  [caches.images]
    maxAge = -1
  [caches.getresource]
    maxAge = "24h"
```

### partialCached

```html
{{/* LENTO: se re-ejecuta en cada pagina */}}
{{ partial "expensive-sidebar.html" . }}

{{/* RAPIDO: cached por Section */}}
{{ partialCached "expensive-sidebar.html" . .Section }}

{{/* RAPIDO: cached por Language + Section */}}
{{ partialCached "nav.html" . .Language.Lang .Section }}
```

### Image Processing Cache

Las imagenes procesadas se cachean en `resources/`. Commitea este directorio para evitar re-procesamiento en CI:

```gitignore
# .gitignore — NO ignorar resources
# resources/   # Comentado: queremos commitear el cache
```

### Lazy Image Processing

```html
{{/* Solo procesa cuando se accede al .RelPermalink */}}
{{ $img := .Resources.GetMatch "hero.*" }}
{{ $resized := $img.Resize "800x webp" }}
{{/* La imagen no se procesa hasta aqui: */}}
<img src="{{ $resized.RelPermalink }}">
```

### Build Stats para PurgeCSS

```toml
[build.buildStats]
  enable = true
```

Genera `hugo_stats.json` con clases CSS usadas — util para PurgeCSS/Tailwind purge.

## Frontend Performance

### Lazy Loading de Imagenes

```html
<img src="{{ $img.RelPermalink }}"
     loading="lazy"
     decoding="async"
     width="{{ $img.Width }}"
     height="{{ $img.Height }}"
     alt="{{ .Title }}">
```

SIEMPRE incluir `width` y `height` para evitar CLS (Cumulative Layout Shift).

### Responsive Images con srcset

```html
{{ $img := .Resources.GetMatch "hero.*" }}
{{ $xs := $img.Resize "480x webp" }}
{{ $sm := $img.Resize "768x webp" }}
{{ $md := $img.Resize "1024x webp" }}
{{ $lg := $img.Resize "1440x webp" }}

<picture>
  <source type="image/webp"
    srcset="{{ $xs.RelPermalink }} 480w,
            {{ $sm.RelPermalink }} 768w,
            {{ $md.RelPermalink }} 1024w,
            {{ $lg.RelPermalink }} 1440w"
    sizes="(max-width: 480px) 480px,
           (max-width: 768px) 768px,
           (max-width: 1024px) 1024px,
           1440px">
  <img src="{{ ($img.Resize "1024x").RelPermalink }}"
       alt="{{ .Title }}"
       width="{{ $img.Width }}"
       height="{{ $img.Height }}"
       loading="lazy">
</picture>
```

### Critical CSS Inline

```html
{{/* Inline critical CSS, defer non-critical */}}
{{ $critical := resources.Get "css/critical.css" | resources.Minify }}
<style>{{ $critical.Content | safeCSS }}</style>

{{ $main := resources.Get "css/main.css" | resources.Minify | resources.Fingerprint }}
<link rel="preload" href="{{ $main.RelPermalink }}" as="style"
      onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="{{ $main.RelPermalink }}"></noscript>
```

### Preload/Prefetch

```html
{{/* Preload fuentes */}}
{{ range .Site.Data.fonts }}
  <link rel="preload" href="{{ .url }}" as="font" type="font/woff2" crossorigin>
{{ end }}

{{/* Prefetch paginas probables */}}
{{ with .NextInSection }}
  <link rel="prefetch" href="{{ .RelPermalink }}">
{{ end }}

{{/* DNS prefetch para CDNs */}}
<link rel="dns-prefetch" href="//cdn.example.com">
```

### JS Diferido

```html
{{/* Module (defer por defecto) */}}
<script type="module" src="{{ $js.RelPermalink }}"></script>

{{/* Legacy con defer */}}
<script defer src="{{ $js.RelPermalink }}"></script>

{{/* Inline solo si es pequeno */}}
{{ if lt (len $js.Content) 2000 }}
  <script>{{ $js.Content | safeJS }}</script>
{{ else }}
  <script defer src="{{ $js.RelPermalink }}"></script>
{{ end }}
```

## SEO

### Meta Tags Template

```html
{{/* layouts/_partials/meta/seo.html */}}
<title>{{ if .IsHome }}{{ .Site.Title }}{{ else }}{{ .Title }} — {{ .Site.Title }}{{ end }}</title>
<meta name="description" content="{{ with .Description }}{{ . }}{{ else }}{{ .Summary | plainify | truncate 155 }}{{ end }}">
{{ with .Params.keywords }}<meta name="keywords" content="{{ delimit . ", " }}">{{ end }}
<link rel="canonical" href="{{ .Permalink }}">

{{/* Hreflang para multilingue */}}
{{ if .IsTranslated }}
  {{ range .Translations }}
    <link rel="alternate" hreflang="{{ .Lang }}" href="{{ .Permalink }}">
  {{ end }}
  <link rel="alternate" hreflang="{{ .Lang }}" href="{{ .Permalink }}">
{{ end }}
```

### Open Graph

```html
{{/* layouts/_partials/meta/opengraph.html */}}
<meta property="og:title" content="{{ .Title }}">
<meta property="og:description" content="{{ with .Description }}{{ . }}{{ else }}{{ .Summary | plainify | truncate 200 }}{{ end }}">
<meta property="og:url" content="{{ .Permalink }}">
<meta property="og:site_name" content="{{ .Site.Title }}">
<meta property="og:type" content="{{ if .IsPage }}article{{ else }}website{{ end }}">
<meta property="og:locale" content="{{ .Site.Language.Lang }}">
{{ with .Params.images }}
  {{ range first 1 . }}
    <meta property="og:image" content="{{ . | absURL }}">
  {{ end }}
{{ else }}
  {{ $img := .Resources.GetMatch "hero.*" }}
  {{ with $img }}
    {{ $og := .Fill "1200x630 center" }}
    <meta property="og:image" content="{{ $og.Permalink }}">
    <meta property="og:image:width" content="1200">
    <meta property="og:image:height" content="630">
  {{ end }}
{{ end }}
{{ if .IsPage }}
  <meta property="article:published_time" content="{{ .Date.Format "2006-01-02T15:04:05Z07:00" }}">
  {{ with .Lastmod }}<meta property="article:modified_time" content="{{ .Format "2006-01-02T15:04:05Z07:00" }}">{{ end }}
  {{ range .GetTerms "tags" }}
    <meta property="article:tag" content="{{ .LinkTitle }}">
  {{ end }}
{{ end }}
```

### Twitter Cards

```html
{{/* layouts/_partials/meta/twitter.html */}}
<meta name="twitter:card" content="summary_large_image">
{{ with .Site.Params.social.twitter }}<meta name="twitter:site" content="@{{ . }}">{{ end }}
<meta name="twitter:title" content="{{ .Title }}">
<meta name="twitter:description" content="{{ with .Description }}{{ . }}{{ else }}{{ .Summary | plainify | truncate 200 }}{{ end }}">
{{ $img := .Resources.GetMatch "hero.*" }}
{{ with $img }}
  {{ $twitter := .Fill "1200x600 center" }}
  <meta name="twitter:image" content="{{ $twitter.Permalink }}">
{{ end }}
```

### JSON-LD Structured Data

```html
{{/* layouts/_partials/meta/jsonld.html */}}
{{ if .IsPage }}
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": {{ .Title | jsonify }},
  "datePublished": "{{ .Date.Format "2006-01-02T15:04:05Z07:00" }}",
  "dateModified": "{{ .Lastmod.Format "2006-01-02T15:04:05Z07:00" }}",
  "description": {{ with .Description }}{{ . | jsonify }}{{ else }}{{ .Summary | plainify | truncate 200 | jsonify }}{{ end }},
  "url": {{ .Permalink | jsonify }},
  "author": {
    "@type": "Person",
    "name": {{ with .Params.author }}{{ . | jsonify }}{{ else }}{{ .Site.Params.author | jsonify }}{{ end }}
  },
  "publisher": {
    "@type": "Organization",
    "name": {{ .Site.Title | jsonify }}
  }
  {{ with .Resources.GetMatch "hero.*" }}
  ,"image": {
    "@type": "ImageObject",
    "url": {{ .Permalink | jsonify }},
    "width": {{ .Width }},
    "height": {{ .Height }}
  }
  {{ end }}
}
</script>
{{ end }}
```

### Sitemap

Hugo genera `sitemap.xml` automaticamente.

```toml
# hugo.toml
[sitemap]
  changeFreq = "weekly"
  priority = 0.5
  filename = "sitemap.xml"
```

Override por pagina en front matter:

```yaml
---
sitemap:
  changeFreq: daily
  priority: 0.8
---
```

### robots.txt

```toml
# hugo.toml
enableRobotsTXT = true
```

```html
{{/* layouts/robots.txt */}}
User-agent: *
Allow: /

Sitemap: {{ .Site.BaseURL }}sitemap.xml

{{ if eq hugo.Environment "staging" }}
User-agent: *
Disallow: /
{{ end }}
```

### RSS Feed

Hugo genera RSS automaticamente. Personalizar:

```html
{{/* layouts/_default/rss.xml */}}
{{- printf "<?xml version=\"1.0\" encoding=\"utf-8\" standalone=\"yes\"?>" | safeHTML }}
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>{{ .Site.Title }}</title>
    <link>{{ .Permalink }}</link>
    <description>{{ .Site.Params.description }}</description>
    <language>{{ .Site.Language.Lang }}</language>
    <lastBuildDate>{{ now.Format "Mon, 02 Jan 2006 15:04:05 -0700" }}</lastBuildDate>
    <atom:link href="{{ .Permalink }}" rel="self" type="application/rss+xml" />
    {{ range first 20 .Pages }}
    <item>
      <title>{{ .Title }}</title>
      <link>{{ .Permalink }}</link>
      <pubDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" }}</pubDate>
      <guid>{{ .Permalink }}</guid>
      <description>{{ .Summary | html }}</description>
    </item>
    {{ end }}
  </channel>
</rss>
```

## Lighthouse Optimization Checklist

- [ ] Imagenes en WebP/AVIF con lazy loading
- [ ] Width/height en todas las `<img>` (evitar CLS)
- [ ] CSS critico inline, resto defer
- [ ] JS con `type="module"` o `defer`
- [ ] Fonts preloaded
- [ ] Gzip/Brotli en server
- [ ] Cache headers (1 year para fingerprinted assets)
- [ ] Minificacion global (`hugo --minify`)
- [ ] Prefetch/preconnect para recursos externos
- [ ] Eliminar JS innecesario
- [ ] Responsive images con srcset
- [ ] Service Worker (si aplica)
