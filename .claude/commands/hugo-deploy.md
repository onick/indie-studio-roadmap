# /hugo-deploy — Preparar Deployment

Eres el **Hugo Expert**. Prepara el sitio Hugo para deployment.

## Instrucciones

### Uso

```
/hugo-deploy setup [provider]   — Configurar deployment
/hugo-deploy check              — Pre-deploy checklist
/hugo-deploy optimize           — Optimizar para produccion
```

### Setup — Configurar Deployment

#### Netlify

1. Crea `netlify.toml` con:
   - Build command, publish dir, Hugo version
   - Deploy preview config
   - Headers de seguridad y cache
   - Redirects si necesarios
2. Verifica que `baseURL` es correcto.
3. Agrega `.nvmrc` con version de Node si usa npm.

#### Vercel

1. Crea `vercel.json` con config.
2. Agrega Hugo al build (framework preset o custom build).
3. Configura headers y redirects.

#### Cloudflare Pages

1. Configura build settings (framework: Hugo).
2. Crea `_headers` y `_redirects` en `static/`.
3. Configura environment variables.

#### GitHub Pages

1. Crea `.github/workflows/hugo.yml` con GitHub Actions.
2. Configura Pages settings (source: GitHub Actions).
3. Usa `hugo_extended` en el workflow.

#### VPS (Nginx)

1. Crea configuracion Nginx.
2. Crea script de deploy (`deploy.sh`).
3. Configura SSL con Let's Encrypt.
4. Configura cache headers.

### Check — Pre-deploy Checklist

Ejecuta verificaciones automaticas:

```
================================================
  Pre-Deploy Checklist
================================================

Build:
  [✅/❌] hugo --environment production succeeds
  [✅/❌] No build warnings
  [✅/❌] Build time < [X]s

Content:
  [✅/❌] No draft pages in output
  [✅/❌] No future-dated pages
  [✅/❌] No expired pages
  [✅/❌] All internal links valid
  [✅/❌] No broken image references

SEO:
  [✅/❌] <title> on all pages
  [✅/❌] Meta description on all pages
  [✅/❌] Open Graph tags present
  [✅/❌] Canonical URLs set
  [✅/❌] sitemap.xml generated
  [✅/❌] robots.txt correct
  [✅/❌] Hreflang tags (if multilingual)

Performance:
  [✅/❌] CSS minified + fingerprinted
  [✅/❌] JS minified + fingerprinted
  [✅/❌] Images in WebP/AVIF
  [✅/❌] Lazy loading on images
  [✅/❌] Width/height on images

Security:
  [✅/❌] HTTPS configured
  [✅/❌] Security headers set
  [✅/❌] No sensitive data in output
  [✅/❌] enableInlineShortcodes = false

Files:
  [✅/❌] favicon.ico present
  [✅/❌] 404.html present
  [✅/❌] No unnecessary files in public/
```

### Optimize — Optimizar para Produccion

1. **Build flags**: `hugo --gc --minify --environment production`
2. **Asset pipeline**: Verificar minify + fingerprint + SRI
3. **Images**: Verificar processing, formatos modernos
4. **HTML minify**: Configurar en `hugo.toml`
5. **Cache strategy**: Static assets con max-age immutable
6. **Gzip/Brotli**: Verificar en server
7. **Output**: Reportar tamano total

### Reglas
- SIEMPRE verifica `baseURL` antes de deploy.
- NUNCA deploys con `buildDrafts = true`.
- Staging primero, produccion despues.
- Cada deploy debe ser reproducible (misma Hugo version, mismos modulos).
- Guarda Hugo version en CI config (no "latest").
