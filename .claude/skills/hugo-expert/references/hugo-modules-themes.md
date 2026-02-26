# Hugo Modules & Themes — Reference

## Hugo Modules

Hugo Modules usa Go Modules bajo el capó para gestionar dependencias (temas, componentes, contenido).

### Prerequisitos

- Go 1.18+ instalado
- Git instalado

### Inicializar un Modulo

```bash
# Para un sitio Hugo
hugo mod init github.com/user/my-site

# Para un tema Hugo
hugo mod init github.com/user/my-theme
```

Esto crea `go.mod` en la raiz del proyecto.

### Importar Modulos

```toml
# hugo.toml
[module]
  [[module.imports]]
    path = "github.com/user/hugo-theme-awesome"
  [[module.imports]]
    path = "github.com/user/hugo-shortcodes-collection"
  [[module.imports]]
    path = "github.com/user/hugo-seo-module"
```

**Precedencia**: Los imports se procesan de arriba a abajo. El primero que define un archivo "gana".

### Mounts (Union File System)

Los mounts permiten mapear directorios de un modulo a ubicaciones especificas del sitio.

```toml
[module]
  [[module.imports]]
    path = "github.com/user/my-theme"
    [[module.imports.mounts]]
      source = "layouts"
      target = "layouts"
    [[module.imports.mounts]]
      source = "assets"
      target = "assets"
    [[module.imports.mounts]]
      source = "static"
      target = "static"

  # Mounts del proyecto (siempre declararlos si usas mounts de imports)
  [[module.mounts]]
    source = "content"
    target = "content"
  [[module.mounts]]
    source = "layouts"
    target = "layouts"
  [[module.mounts]]
    source = "assets"
    target = "assets"
  [[module.mounts]]
    source = "static"
    target = "static"
  [[module.mounts]]
    source = "data"
    target = "data"
  [[module.mounts]]
    source = "i18n"
    target = "i18n"
```

### Mounts Avanzados

```toml
# Montar solo un subdirectorio
[[module.imports]]
  path = "github.com/user/icons"
  [[module.imports.mounts]]
    source = "svg/social"
    target = "assets/icons/social"

# Montar contenido externo
[[module.imports]]
  path = "github.com/user/shared-content"
  [[module.imports.mounts]]
    source = "docs"
    target = "content/shared-docs"

# Excluir archivos
[[module.imports]]
  path = "github.com/user/theme"
  [[module.imports.mounts]]
    source = "layouts"
    target = "layouts"
    excludeFiles = ["_default/single.html"]
```

### Vendoring

```bash
# Copiar modulos a _vendor/
hugo mod vendor

# Build usando vendor (no descarga)
HUGO_MODULE_VENDOR=true hugo
```

El directorio `_vendor/` contiene copias locales de los modulos. Para override, crea el archivo correspondiente en tu proyecto (NO modifiques `_vendor/`).

### Comandos de Modulos

```bash
hugo mod init [path]       # Inicializar modulo
hugo mod get -u            # Actualizar todos los modulos
hugo mod get -u ./...      # Actualizar recursivamente
hugo mod get github.com/user/theme@v1.2.0  # Version especifica
hugo mod get github.com/user/theme@latest  # Ultima version
hugo mod tidy              # Limpiar go.mod y go.sum
hugo mod vendor            # Vendorizar
hugo mod verify            # Verificar checksums
hugo mod graph             # Mostrar grafo de dependencias
hugo mod clean             # Limpiar cache del proyecto
hugo mod clean --all       # Limpiar cache global
hugo mod npm pack          # Generar package.json combinado
```

### Replace para Desarrollo Local

```go
// go.mod
module github.com/user/my-site

go 1.21

require github.com/user/my-theme v0.5.0

replace github.com/user/my-theme => ../my-theme-local
```

O via configuracion:

```toml
# hugo.toml
[module]
  replacements = "github.com/user/my-theme -> ../my-theme-local"
```

### Workspace

```go
// .work
go 1.21
use .
use ../my-theme
use ../my-shortcodes
```

Activar: `HUGO_MODULE_WORKSPACE=.work hugo server`

## Temas

### Estructura de un Tema Hugo

```
my-theme/
├── archetypes/
├── assets/
│   ├── css/
│   └── js/
├── data/
├── i18n/
├── layouts/
│   ├── _default/
│   │   ├── baseof.html
│   │   ├── home.html
│   │   ├── list.html
│   │   └── single.html
│   ├── _partials/
│   ├── _shortcodes/
│   └── _markup/
├── static/
├── hugo.toml            # Config del tema (defaults)
├── go.mod               # Hugo Module
├── theme.toml           # Metadata del tema (legacy)
├── LICENSE
└── README.md
```

### Crear un Tema

```bash
hugo new theme my-theme
cd themes/my-theme
hugo mod init github.com/user/my-theme
```

### Usar Tema como Modulo

```toml
# hugo.toml del sitio
[module]
  [[module.imports]]
    path = "github.com/user/my-theme"
```

### Usar Tema Tradicional (sin modules)

```toml
# hugo.toml
theme = "my-theme"
```

El tema debe estar en `themes/my-theme/`.

### Override de Tema

Para override cualquier archivo del tema, crea el mismo archivo en tu proyecto. El proyecto siempre tiene precedencia sobre el tema.

```
my-site/
├── layouts/
│   └── _default/
│       └── single.html    # Override el single.html del tema
└── themes/
    └── my-theme/
        └── layouts/
            └── _default/
                └── single.html  # Este se ignora
```

### Theme Components

Un sitio puede importar multiples componentes de tema:

```toml
[module]
  [[module.imports]]
    path = "github.com/user/base-theme"     # Tema principal
  [[module.imports]]
    path = "github.com/user/seo-component"  # Solo agrega SEO templates
  [[module.imports]]
    path = "github.com/user/analytics"      # Solo agrega analytics
```

### Parametros del Tema

```toml
# hugo.toml del sitio (override params del tema)
[params]
  [params.theme]
    colorScheme = "dark"
    showToc = true
    maxRelatedPosts = 5

  [params.footer]
    showCopyright = true
    copyrightText = "2025 My Company"
```

```html
{{/* En template del tema */}}
{{ with .Site.Params.theme.colorScheme }}
  <body class="scheme-{{ . }}">
{{ else }}
  <body class="scheme-light">
{{ end }}
```

## Patron: Modulo de Shortcodes Reutilizable

```toml
# go.mod del modulo
module github.com/user/hugo-shortcodes

go 1.21
```

```
hugo-shortcodes/
├── layouts/
│   └── _shortcodes/
│       ├── alert.html
│       ├── tabs.html
│       ├── accordion.html
│       └── youtube-lite.html
├── assets/
│   └── css/
│       └── shortcodes.css
├── go.mod
└── README.md
```

```toml
# hugo.toml del sitio que consume
[module]
  [[module.imports]]
    path = "github.com/user/hugo-shortcodes"
    [[module.imports.mounts]]
      source = "layouts/_shortcodes"
      target = "layouts/_shortcodes"
    [[module.imports.mounts]]
      source = "assets"
      target = "assets"
```
