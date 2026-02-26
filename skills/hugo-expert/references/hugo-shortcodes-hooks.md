# Hugo Shortcodes & Render Hooks — Reference

## Shortcodes Built-in

### figure

```markdown
{{</* figure src="image.jpg" title="Caption" alt="Alt text" width="600" */>}}
```

### highlight (syntax highlighting)

```markdown
{{</* highlight go "linenos=table,hl_lines=3 5" */>}}
package main

import "fmt"

func main() {
    fmt.Println("Hello")
}
{{</* /highlight */>}}
```

### instagram

```markdown
{{</* instagram CxOWiQNP2MO */>}}
```

### param (acceder a parametros)

```markdown
{{</* param "description" */>}}
```

### ref / relref (links internos)

```markdown
[Mi Post]({{</* ref "posts/my-post.md" */>}})
[Relativo]({{</* relref "about" */>}})
```

### youtube

```markdown
{{</* youtube w7Ft2ymGmfc */>}}
```

### vimeo

```markdown
{{</* vimeo 146022717 */>}}
```

### details

```markdown
{{</* details "Click para expandir" */>}}
Contenido oculto aqui.
{{</* /details */>}}
```

### qr

```markdown
{{</* qr "https://gohugo.io" */>}}
```

## Shortcodes Custom

### Ubicacion

`layouts/_shortcodes/[name].html`

### Argumentos posicionales

```html
{{/* layouts/_shortcodes/youtube-lite.html */}}
{{ $id := .Get 0 }}
{{ $title := .Get 1 | default "Video" }}
<lite-youtube videoid="{{ $id }}" playlabel="{{ $title }}"></lite-youtube>
```

```markdown
{{</* youtube-lite "w7Ft2ymGmfc" "Mi Video" */>}}
```

### Argumentos nombrados

```html
{{/* layouts/_shortcodes/alert.html */}}
{{ $type := .Get "type" | default "info" }}
{{ $title := .Get "title" }}
<div class="alert alert-{{ $type }}">
  {{ with $title }}<strong>{{ . }}</strong>{{ end }}
  {{ .Inner | markdownify }}
</div>
```

```markdown
{{</* alert type="warning" title="Cuidado" */>}}
Este es un mensaje de advertencia con **markdown**.
{{</* /alert */>}}
```

### Con inner content

```html
{{/* layouts/_shortcodes/callout.html */}}
{{ $type := .Get "type" | default "note" }}
{{ $icon := dict "note" "📝" "tip" "💡" "warning" "⚠️" "danger" "🚨" }}
<div class="callout callout-{{ $type }}">
  <span class="callout-icon">{{ index $icon $type }}</span>
  <div class="callout-content">
    {{ .Inner | markdownify }}
  </div>
</div>
```

### Notacion Markdown vs Standard

```markdown
{{%/* con-markdown */%}}
**Este Markdown** se procesa _antes_ del shortcode.
{{%/* /con-markdown */%}}

{{</* sin-markdown */>}}
**Este texto** se pasa raw al shortcode.
{{</* /sin-markdown */>}}
```

Usa `{{% %}}` cuando el inner content tiene Markdown que debe procesarse.
Usa `{{< >}}` cuando el shortcode maneja el rendering.

### Shortcode con acceso a Page

```html
{{/* layouts/_shortcodes/related-posts.html */}}
{{ $tags := .Page.Params.tags }}
{{ $current := .Page }}
{{ range first 3 (where .Page.Site.RegularPages "Params.tags" "intersect" $tags) }}
  {{ if ne .Permalink $current.Permalink }}
    <a href="{{ .RelPermalink }}">{{ .Title }}</a>
  {{ end }}
{{ end }}
```

### Shortcode con recursos de pagina

```html
{{/* layouts/_shortcodes/gallery.html */}}
{{ $images := .Page.Resources.Match (printf "%s/*" (.Get "folder" | default "gallery")) }}
<div class="gallery">
  {{ range $images }}
    {{ $thumb := .Fill "300x300 center" }}
    <a href="{{ .RelPermalink }}" data-lightbox="gallery">
      <img src="{{ $thumb.RelPermalink }}" alt="{{ .Title }}" loading="lazy">
    </a>
  {{ end }}
</div>
```

```markdown
{{</* gallery folder="photos" */>}}
```

### Shortcodes anidados

```html
{{/* layouts/_shortcodes/tabs.html */}}
<div class="tabs" x-data="{ active: 0 }">
  <div class="tab-buttons">
    {{ range $i, $e := .Inner | split "<!-- tab -->" }}
      {{ if gt (len (strings.TrimSpace $e)) 0 }}
        <button @click="active = {{ $i }}" :class="active === {{ $i }} && 'active'">
          Tab {{ add $i 1 }}
        </button>
      {{ end }}
    {{ end }}
  </div>
  <div class="tab-content">
    {{ .Inner }}
  </div>
</div>
```

### Inline Shortcodes (opt-in)

```toml
# hugo.toml
enableInlineShortcodes = true
```

```markdown
Hoy es {{</* time.Format "Monday, January 2, 2006" now */>}}.
```

Riesgo de seguridad: solo habilitar si controlas todo el contenido.

## Render Hooks

Templates que interceptan el rendering de Markdown a HTML.

### Ubicacion

```
layouts/_markup/
├── render-image.html              # Todas las imagenes
├── render-image-png.html          # Solo PNG
├── render-link.html               # Todos los links
├── render-heading.html            # Todos los headings
├── render-codeblock.html          # Code blocks genericos
├── render-codeblock-mermaid.html  # Solo ```mermaid
├── render-codeblock-chart.html    # Solo ```chart
├── render-blockquote.html         # Blockquotes
├── render-blockquote-alert.html   # Obsidian-style callouts
└── render-table.html              # Tablas
```

### Context Variables (disponibles en render hooks)

**Image render hook:**
- `.Destination` — URL de la imagen
- `.Text` — Alt text
- `.Title` — Title attribute
- `.Page` — La pagina que contiene la imagen
- `.Ordinal` — Indice (0-based) del elemento
- `.IsBlock` — Si es bloque (no inline)

**Link render hook:**
- `.Destination` — URL del link
- `.Text` — Texto del link
- `.Title` — Title attribute
- `.Page` — La pagina que contiene el link

**Heading render hook:**
- `.Level` — Nivel (1-6)
- `.Anchor` — ID generado
- `.Text` — Texto del heading
- `.Page` — La pagina

**Codeblock render hook:**
- `.Type` — Lenguaje (go, js, mermaid, etc.)
- `.Inner` — Contenido del code block
- `.Attributes` — Atributos del fence
- `.Options` — Highlight options
- `.Ordinal` — Indice
- `.Page` — La pagina
- `.Position` — Posicion en el archivo (para errores)

**Blockquote render hook:**
- `.Type` — Tipo de alerta (para callouts)
- `.AlertTitle` — Titulo de la alerta
- `.Inner` — Contenido del blockquote
- `.Page` — La pagina

### Image Render Hook — Responsive + WebP

```html
{{/* layouts/_markup/render-image.html */}}
{{ $img := .Page.Resources.GetMatch .Destination }}
{{ if $img }}
  {{ $small := $img.Resize "480x webp" }}
  {{ $medium := $img.Resize "800x webp" }}
  {{ $large := $img.Resize "1200x webp" }}
  {{ $fallback := $img.Resize "800x" }}
  <figure>
    <picture>
      <source
        type="image/webp"
        srcset="{{ $small.RelPermalink }} 480w,
                {{ $medium.RelPermalink }} 800w,
                {{ $large.RelPermalink }} 1200w"
        sizes="(max-width: 480px) 480px, (max-width: 800px) 800px, 1200px">
      <img
        src="{{ $fallback.RelPermalink }}"
        alt="{{ .Text }}"
        width="{{ $fallback.Width }}"
        height="{{ $fallback.Height }}"
        loading="lazy"
        decoding="async">
    </picture>
    {{ with .Title }}<figcaption>{{ . | markdownify }}</figcaption>{{ end }}
  </figure>
{{ else }}
  <img src="{{ .Destination | safeURL }}" alt="{{ .Text }}" loading="lazy">
{{ end }}
```

### Link Render Hook — External Links

```html
{{/* layouts/_markup/render-link.html */}}
{{ $isExternal := hasPrefix .Destination "http" }}
<a href="{{ .Destination | safeURL }}"
  {{ if $isExternal }}
    target="_blank"
    rel="noopener noreferrer"
    class="external-link"
  {{ end }}
  {{ with .Title }}title="{{ . }}"{{ end }}>
  {{- .Text -}}
  {{ if $isExternal }}
    <svg class="external-icon" viewBox="0 0 16 16" width="12" height="12">
      <path d="M3.75 2h3.5a.75.75 0 0 1 0 1.5H4.56l7.22 7.22a.75.75 0 1 1-1.06 1.06L3.5 4.56v2.69a.75.75 0 0 1-1.5 0v-3.5A1.75 1.75 0 0 1 3.75 2z"/>
    </svg>
  {{ end -}}
</a>
```

### Heading Render Hook — Anchor Links

```html
{{/* layouts/_markup/render-heading.html */}}
<h{{ .Level }} id="{{ .Anchor }}" class="heading">
  {{ .Text }}
  <a href="#{{ .Anchor }}" class="heading-anchor" aria-hidden="true">#</a>
</h{{ .Level }}>
```

### Codeblock Render Hook — Mermaid Diagrams

```html
{{/* layouts/_markup/render-codeblock-mermaid.html */}}
<pre class="mermaid">
  {{- .Inner | safeHTML -}}
</pre>
{{ .Page.Store.Set "hasMermaid" true }}
```

```html
{{/* En baseof.html */}}
{{ if .Store.Get "hasMermaid" }}
  <script type="module">
    import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.esm.min.mjs';
    mermaid.initialize({ startOnLoad: true, theme: 'neutral' });
  </script>
{{ end }}
```

### Blockquote Render Hook — Callouts (Obsidian-style)

```html
{{/* layouts/_markup/render-blockquote-alert.html */}}
{{ $icons := dict
  "note" "📝"
  "tip" "💡"
  "important" "❗"
  "warning" "⚠️"
  "caution" "🚨"
}}
<div class="callout callout-{{ .Type }}">
  <div class="callout-header">
    <span>{{ index $icons .Type | default "📌" }}</span>
    <strong>{{ with .AlertTitle }}{{ . }}{{ else }}{{ .Type | strings.Title }}{{ end }}</strong>
  </div>
  <div class="callout-body">
    {{ .Inner }}
  </div>
</div>
```

Uso en Markdown:

```markdown
> [!warning] Cuidado
> Este es un callout de advertencia.
```

### Table Render Hook

```html
{{/* layouts/_markup/render-table.html */}}
<div class="table-wrapper" role="region" tabindex="0" aria-label="Scrollable table">
  <table>
    <thead>
      {{ range .THead }}
        <tr>
          {{ range . }}
            <th {{ with .Alignment }}style="text-align: {{ . }}"{{ end }}>
              {{ .Text }}
            </th>
          {{ end }}
        </tr>
      {{ end }}
    </thead>
    <tbody>
      {{ range .TBody }}
        <tr>
          {{ range . }}
            <td {{ with .Alignment }}style="text-align: {{ . }}"{{ end }}>
              {{ .Text }}
            </td>
          {{ end }}
        </tr>
      {{ end }}
    </tbody>
  </table>
</div>
```
