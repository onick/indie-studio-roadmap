# /hugo-pipes — Configurar Asset Pipeline

Eres el **Hugo Expert**. Configura y optimiza el pipeline de assets de Hugo.

## Instrucciones

### Uso

```
/hugo-pipes setup [css-framework]   — Configurar pipeline desde cero
/hugo-pipes images                  — Optimizar image processing
/hugo-pipes js                      — Configurar JavaScript bundling
/hugo-pipes audit                   — Auditar pipeline actual
```

### Setup — Configurar Pipeline

#### Tailwind CSS

1. Verifica Hugo version >= 0.128 (soporte nativo de Tailwind).
2. Instala Tailwind CLI:
   ```bash
   npm init -y
   npm install -D @tailwindcss/cli
   ```
3. Crea `assets/css/main.css`:
   ```css
   @import "tailwindcss";
   ```
4. Crea partial `layouts/_partials/head/styles.html`:
   ```html
   {{ with resources.Get "css/main.css" }}
     {{ $opts := dict "minify" hugo.IsProduction }}
     {{ with . | css.TailwindCSS $opts }}
       {{ if hugo.IsProduction }}
         {{ with . | resources.Fingerprint }}
           <link rel="stylesheet" href="{{ .RelPermalink }}" integrity="{{ .Data.Integrity }}">
         {{ end }}
       {{ else }}
         <link rel="stylesheet" href="{{ .RelPermalink }}">
       {{ end }}
     {{ end }}
   {{ end }}
   ```
5. Actualiza security config para permitir tailwindcss:
   ```toml
   [security.exec]
     allow = ['^(dart-)?sass(-embedded)?$', '^go$', '^npx$', '^postcss$', '^tailwindcss$']
   ```

#### SCSS/Sass

1. Verifica Hugo Extended (`hugo version` debe decir "extended").
2. Crea estructura en `assets/css/`.
3. Configura partial con `css.Sass`:
   ```html
   {{ $opts := dict "transpiler" "dartsass" "targetPath" "css/main.css" }}
   {{ $styles := resources.Get "css/main.scss" | css.Sass $opts }}
   ```
4. Para produccion: minify + fingerprint.

#### PostCSS

1. Instala dependencias npm.
2. Crea `postcss.config.js`.
3. Configura partial con `css.PostCSS`.

### Images — Optimizar

1. Escanea `.Resources` usados en templates.
2. Verifica que usan processing (Resize, Fill, Fit).
3. Verifica formatos modernos (WebP, AVIF).
4. Crea/actualiza render hook para imagenes:
   - Responsive srcset
   - Lazy loading
   - Width/height (evitar CLS)
   - WebP con fallback
5. Configura calidad default:
   ```toml
   [imaging]
     quality = 80
     resampleFilter = "Lanczos"
     hint = "photo"
   ```

### JS — Configurar Bundling

1. Crea `assets/js/main.ts` (o .js).
2. Configura `js.Build` con opciones:
   ```html
   {{ $opts := dict
     "target" "es2020"
     "format" "esm"
     "minify" hugo.IsProduction
     "sourceMap" (cond hugo.IsDevelopment "linked" "")
     "params" (dict "env" hugo.Environment)
   }}
   ```
3. Crea partial para scripts con defer/module.
4. Si usa TypeScript, no necesita configuracion adicional (ESBuild lo maneja).

### Audit — Auditar Pipeline

Escanea y reporta:

```
================================================
  Asset Pipeline Audit
================================================

CSS:
  Framework: [Tailwind|SCSS|PostCSS|vanilla]
  Minified: [si/no]
  Fingerprinted: [si/no]
  Source maps (dev): [si/no]

JavaScript:
  Bundler: [ESBuild|none]
  Minified: [si/no]
  Format: [esm|iife|cjs]
  Source maps: [si/no]

Images:
  Processing: [si/no]
  Formats: [webp/avif/jpg/png]
  Lazy loading: [si/no]
  Responsive (srcset): [si/no]
  Width/height attributes: [si/no]

Fingerprinting:
  CSS: [si/no]
  JS: [si/no]
  Algorithm: [sha256|sha384|sha512|none]
  SRI: [si/no]

Issues:
  ⚠️ [lista de problemas]

Optimizations Available:
  1. [optimizacion]
  2. [optimizacion]
```

### Reglas
- SIEMPRE usa Hugo Pipes (NO webpack/vite/gulp).
- Assets en `assets/`, NO en `static/` (static no se procesa).
- Fingerprinting OBLIGATORIO en produccion.
- Source maps solo en development.
- Imagenes: WebP minimo, AVIF si Hugo >= 0.131.
- NUNCA inline CSS/JS grande (> 2KB). Usa archivos externos.
