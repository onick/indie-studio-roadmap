# Hugo Deployment — Reference

## Build para Produccion

```bash
# Build optimizado
hugo --minify --gc --environment production

# Con baseURL override
hugo --minify --baseURL "https://example.com/"

# Verificar output
ls -la public/
du -sh public/
```

### Flags de build utiles

| Flag | Descripcion |
|------|-------------|
| `--minify` | Minificar HTML, CSS, JS, JSON, SVG, XML |
| `--gc` | Garbage collect: limpiar cache obsoleto |
| `--cleanDestinationDir` | Borrar archivos viejos en public/ |
| `--environment production` | Ambiente de produccion |
| `--baseURL "url"` | Override baseURL |
| `--buildDrafts` | Incluir drafts |
| `--buildFuture` | Incluir contenido futuro |
| `--verbose` | Output detallado |
| `--logLevel info` | Nivel de log |

## Netlify

### netlify.toml

```toml
[build]
  publish = "public"
  command = "hugo --gc --minify"

[build.environment]
  HUGO_VERSION = "0.140.0"
  HUGO_ENV = "production"
  HUGO_ENABLEGITINFO = "true"
  NODE_VERSION = "20"
  GO_VERSION = "1.22"

[context.deploy-preview]
  command = "hugo --gc --minify -b $DEPLOY_PRIME_URL"

[context.branch-deploy]
  command = "hugo --gc --minify -b $DEPLOY_PRIME_URL"

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"
    Content-Security-Policy = "default-src 'self'; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-inline'; img-src 'self' data: https:;"

[[headers]]
  for = "/css/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
  for = "/js/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"

[[redirects]]
  from = "/old-path/*"
  to = "/new-path/:splat"
  status = 301

[[plugins]]
  package = "@netlify/plugin-lighthouse"
```

## Vercel

### vercel.json

```json
{
  "framework": "hugo",
  "buildCommand": "hugo --gc --minify",
  "outputDirectory": "public",
  "installCommand": "yum install -y golang",
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "X-Content-Type-Options", "value": "nosniff" }
      ]
    },
    {
      "source": "/css/(.*)",
      "headers": [
        { "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }
      ]
    }
  ],
  "redirects": [
    { "source": "/old", "destination": "/new", "permanent": true }
  ]
}
```

## Cloudflare Pages

### Build settings

- **Framework preset**: Hugo
- **Build command**: `hugo --gc --minify`
- **Build output directory**: `public`
- **Environment variables**:
  - `HUGO_VERSION` = `0.140.0`
  - `GO_VERSION` = `1.22`
  - `NODE_VERSION` = `20`

### _headers

```
/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff

/css/*
  Cache-Control: public, max-age=31536000, immutable

/js/*
  Cache-Control: public, max-age=31536000, immutable
```

### _redirects

```
/old-url /new-url 301
/blog/old/* /posts/:splat 301
```

## GitHub Pages

### GitHub Actions Workflow

```yaml
# .github/workflows/hugo.yml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.140.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
          sudo dpkg -i ${{ runner.temp }}/hugo.deb

      - name: Install Dart Sass
        run: sudo snap install dart-sass

      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4

      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"

      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## VPS con Nginx

### Nginx config

```nginx
server {
    listen 443 ssl http2;
    server_name example.com;
    root /var/www/example.com/public;
    index index.html;

    # SSL
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    # Security headers
    add_header X-Frame-Options "DENY" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # Cache static assets
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg|webp|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # SPA-style fallback (si usas client-side routing)
    location / {
        try_files $uri $uri/ =404;
    }

    # Custom 404
    error_page 404 /404.html;

    # Gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
    gzip_min_length 1000;
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}
```

### Deploy script

```bash
#!/bin/bash
# deploy.sh
set -euo pipefail

SITE_DIR="/var/www/example.com"
REPO_DIR="/opt/hugo-site"

cd "$REPO_DIR"
git pull origin main
hugo --gc --minify --environment production
rsync -av --delete public/ "$SITE_DIR/public/"
echo "Deployed at $(date)"
```

## Hugo Deploy (built-in)

Hugo tiene deploy nativo a S3, GCS, y Azure Blob.

```toml
# hugo.toml
[deployment]
  [[deployment.targets]]
    name = "production"
    URL = "s3://my-bucket?region=us-east-1"
    cloudFrontDistributionID = "EXXXXXXXXXXXX"

  [[deployment.matchers]]
    pattern = "^.+\\.(js|css|svg|ttf|woff|woff2)$"
    cacheControl = "max-age=31536000, no-transform, public"
    gzip = true

  [[deployment.matchers]]
    pattern = "^.+\\.(png|jpg|gif|webp)$"
    cacheControl = "max-age=31536000, no-transform, public"
    gzip = false

  [[deployment.matchers]]
    pattern = "^.+\\.html$"
    cacheControl = "max-age=0, no-cache, no-store, must-revalidate"
    gzip = true
```

```bash
hugo deploy                    # Deploy a target default
hugo deploy --target production
hugo deploy --dryRun           # Preview sin deploy
hugo deploy --confirm          # Pedir confirmacion
hugo deploy --maxDeletes 100   # Limitar deletes
hugo deploy --invalidateCDN    # Invalidar CloudFront
```

## CI/CD Generico

### Variables de entorno recomendadas

```bash
HUGO_VERSION=0.140.0
HUGO_ENV=production
HUGO_ENABLEGITINFO=true
HUGO_CACHEDIR=/tmp/hugo-cache
GO_VERSION=1.22
NODE_VERSION=20
```

### Checklist pre-deploy

- [ ] `hugo --environment production` sin errores
- [ ] `hugo --gc` para limpiar cache
- [ ] Links rotos verificados
- [ ] Sitemap generado (`public/sitemap.xml`)
- [ ] robots.txt correcto
- [ ] Meta tags (OG, Twitter) presentes
- [ ] Favicon y icons presentes
- [ ] 404 page funciona
- [ ] HTTPS configurado
- [ ] Headers de seguridad
- [ ] Cache headers para assets
- [ ] Gzip/Brotli habilitado
