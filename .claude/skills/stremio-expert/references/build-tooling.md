# Build Tooling — Reference

## Webpack 5 Configuration

**Archivo:** `webpack.config.js`

### Entry Points

```javascript
entry: {
  main: './src/index.js',          // Aplicacion React
  worker: '@stremio/stremio-core-web/worker.js'  // WASM Web Worker
}
```

### Output

```javascript
output: {
  path: path.resolve(__dirname, 'build'),
  filename: `${COMMIT_HASH}/scripts/[name].js`,
  publicPath: '/'
}
```

Estructura de build:
```
build/
├── ${COMMIT_HASH}/
│   ├── scripts/
│   │   ├── main.js
│   │   └── worker.js
│   ├── styles/
│   │   └── main.css
│   ├── binaries/
│   │   └── *.wasm
│   ├── fonts/
│   └── images/
├── favicons/
└── images/
```

### Loaders

| Loader | Archivos | Proposito |
|--------|----------|-----------|
| `babel-loader` | `.js`, `.jsx` | Transpile ES6+/JSX |
| `ts-loader` + `thread-loader` | `.ts`, `.tsx` | TypeScript (paralelo) |
| `less-loader` | `.less` | Preprocesar Less a CSS |
| `css-loader` | `.css` | CSS modules (localIdentName) |
| `postcss-loader` + `cssnano` | CSS | Optimizar/minificar CSS |
| `MiniCssExtractPlugin.loader` | CSS | Extraer CSS a archivos |
| `file-loader` | fonts, images | Copiar archivos estaticos |
| `arraybuffer-loader` | `.wasm` | Cargar WASM como ArrayBuffer |

### CSS Modules (localIdentName)

```javascript
// En development:
localIdentName: '[local]-[hash:base64:5]'
// Resultado: button-xK9j2

// En production:
localIdentName: '[hash:base64:5]'
// Resultado: xK9j2
```

### Plugins

| Plugin | Proposito |
|--------|-----------|
| `HtmlWebpackPlugin` | Genera `index.html` desde template |
| `MiniCssExtractPlugin` | Extrae CSS a archivos separados |
| `TerserPlugin` | Minifica JavaScript |
| `WorkboxPlugin.GenerateSW` | Genera Service Worker para PWA |
| `DefinePlugin` | Inyecta variables de entorno |
| `CopyWebpackPlugin` | Copia archivos estaticos |

### Variables de Entorno

```javascript
new webpack.DefinePlugin({
  'process.env.SENTRY_DSN': JSON.stringify(process.env.SENTRY_DSN),
  'process.env.SERVICE_WORKER_DISABLED': JSON.stringify(process.env.SERVICE_WORKER_DISABLED),
  'process.env.DEBUG': JSON.stringify(mode === 'development'),
  'process.env.VERSION': JSON.stringify(require('./package.json').version),
  'process.env.COMMIT_HASH': JSON.stringify(COMMIT_HASH)
})
```

### Dev Server

```javascript
devServer: {
  host: '0.0.0.0',
  https: true,          // HTTPS obligatorio
  hot: false,           // NO hot reload
  liveReload: false,    // NO live reload — full page refresh
  historyApiFallback: true
}
```

## Package Manager: pnpm

```bash
# Instalar dependencias
pnpm install

# Dev server
pnpm start                    # mode: development
pnpm start-prod              # mode: production

# Build produccion
pnpm build

# Tests
pnpm test

# Linting
pnpm lint

# Scan traducciones
pnpm run scan-translations
```

**Version requerida:** pnpm 10+
**Node.js:** 12+ (`.nvmrc` define la version exacta)

## TypeScript

**Config:** `tsconfig.json`

```json
{
  "compilerOptions": {
    "strict": true,
    "jsx": "react",
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "node",
    "allowJs": true,
    "paths": {
      "stremio/*": ["src/*"]
    },
    "lib": ["ESNext", "DOM", "DOM.Iterable"]
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

**Estado de migracion:** El proyecto esta migrando de JS a TS. Archivos `.ts`/`.tsx` usan ES modules; `.js` usa CommonJS.

## ESLint

**Config:** `eslint.config.mjs` (flat config, ESLint 9)

Reglas clave:
- 4 espacios de indentacion
- Single quotes
- Semicolons obligatorios
- `eqeqeq` (strict equality)
- `no-console` (solo `warn` y `error`)
- TypeScript: `no-explicit-any` deshabilitado

Globals adicionales: `YT`, `FB`, `cast`, `chrome` (APIs externas)

## Docker

**Archivo:** `Dockerfile`

```dockerfile
# Stage 1: Base
FROM node:20-alpine AS base
RUN npm install -g pnpm

# Stage 2: Build
FROM base AS app
WORKDIR /app
COPY . .
RUN pnpm install --frozen-lockfile
RUN pnpm build

# Stage 3: Server
FROM base AS server
WORKDIR /server
RUN npm install express@4

# Stage 4: Final
FROM node:20-alpine AS final
WORKDIR /stremio-web
COPY --from=app /app/build ./build
COPY --from=server /server ./
COPY http_server.js ./
EXPOSE 8080
CMD ["node", "http_server.js"]
```

El servidor HTTP es un Express simple que sirve archivos estaticos.

## CI/CD (GitHub Actions)

**Archivo:** `.github/workflows/build.yml`

Triggers: push a `development`, PRs a `development`, manual

```yaml
steps:
  - checkout
  - pnpm install
  - pnpm build
  - pnpm test
  - pnpm lint
  - deploy to GitHub Pages (branch-specific directory)
```

### PR Previews

Cada PR obtiene un deploy preview en GitHub Pages:
- URL: `https://stremio.github.io/stremio-web/pr-{number}/`
- Se limpia automaticamente al cerrar el PR (`.github/workflows/pages_cleanup.yml`)

## Sentry

**Integracion:** `@sentry/browser`

```javascript
// Solo si SENTRY_DSN esta definido
if (process.env.SENTRY_DSN) {
  Sentry.init({
    dsn: process.env.SENTRY_DSN,
    release: process.env.VERSION,
    environment: process.env.DEBUG ? 'development' : 'production'
  });
}
```
