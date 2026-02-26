# Addon System — Reference

## Que son los Addons de Stremio

Los addons son el corazon de Stremio. Son microservicios HTTP que proveen:
- **Catalogs** — listas de contenido (peliculas, series, anime, etc.)
- **Streams** — fuentes de streaming para un contenido especifico
- **Subtitles** — subtitulos para videos
- **Meta** — metadata extra (posters, descripciones, trailers)

El usuario instala addons y Stremio agrega sus contenidos a la interfaz.

## Addon Manifest

Cada addon expone un manifest JSON en su URL raiz:

```json
{
  "id": "com.example.myaddon",
  "version": "1.0.0",
  "name": "My Addon",
  "description": "Example addon for movies",
  "types": ["movie", "series"],
  "catalogs": [
    {
      "type": "movie",
      "id": "top-movies",
      "name": "Top Movies",
      "extra": [
        { "name": "genre", "options": ["Action", "Comedy", "Drama"] },
        { "name": "search", "isRequired": false }
      ]
    }
  ],
  "resources": ["catalog", "stream", "meta", "subtitles"],
  "idPrefixes": ["tt"],
  "behaviorHints": {
    "adult": false,
    "p2p": false
  }
}
```

## Transport URL

La URL del addon se llama **transport URL**. Es la base URL del servicio HTTP:

```
https://v3-cinemeta.strem.io/manifest.json
```

El core WASM maneja toda la comunicacion con addons. React NO hace fetch directo.

## Endpoints del Addon Protocol

```
GET /manifest.json                              → Manifest
GET /catalog/:type/:id.json                     → Catalogo
GET /catalog/:type/:id/:extra.json              → Catalogo con extras
GET /meta/:type/:id.json                        → Metadata
GET /stream/:type/:id.json                      → Streams
GET /subtitles/:type/:id/:videoId.json          → Subtitulos
```

### Ejemplos

```
GET /catalog/movie/top-movies.json
GET /catalog/movie/top-movies/genre=Action.json
GET /stream/movie/tt1234567.json
GET /subtitles/movie/tt1234567/s01e01.json
```

## Addons en la UI

### Addons Route

**Path:** `#/addons/:type?/:transportUrl?/:catalogId?`

La pagina de Addons permite:
- Ver addons instalados
- Buscar addons en el catalogo central
- Instalar/desinstalar addons
- Ver detalles de cada addon

### AddonDetailsModal

Componente modal que muestra:
- Nombre, version, descripcion
- Tipos de contenido soportados
- Catalogs disponibles
- Boton instalar/desinstalar

### Instalar un Addon via Core

```javascript
// Via dispatch al core WASM
core.transport.dispatch({
  action: 'Ctx',
  args: {
    action: 'InstallAddon',
    args: {
      transportUrl: 'https://addon-url.com/manifest.json'
    }
  }
});
```

### Desinstalar un Addon

```javascript
core.transport.dispatch({
  action: 'Ctx',
  args: {
    action: 'UninstallAddon',
    args: {
      transportUrl: 'https://addon-url.com/manifest.json'
    }
  }
});
```

### Listar Addons Instalados

```javascript
const installedAddons = useModelState({
  model: 'InstalledAddons',
  action: 'Load',
  args: {}
});
```

## Addons Default

Stremio viene con addons preinstalados:
- **Cinemeta** — Metadata de peliculas y series (TMDB/IMDB)
- **OpenSubtitles** — Subtitulos
- **Local Files** — Archivos locales
- **Stremio's Watch Hub** — Catalogo central

## Whitelisted Hosts

En `src/common/CONSTANTS.js` se definen hosts permitidos:

```javascript
const WHITELISTED_HOSTS = [
  'strem.io',
  'stremio.com',
  'app.strem.io',
  'web.stremio.com',
  'www.stremio.com',
  'link.stremio.com',
  // ... mas hosts
];
```

Solo addons de hosts permitidos pueden ejecutar ciertas operaciones privilegiadas.

## Crear un Addon (para desarrollo)

### Estructura basica (Node.js)

```javascript
const { addonBuilder } = require('stremio-addon-sdk');

const manifest = {
  id: 'com.example.myaddon',
  version: '1.0.0',
  name: 'My Addon',
  catalogs: [{ type: 'movie', id: 'mycatalog' }],
  resources: ['catalog', 'stream'],
  types: ['movie']
};

const builder = new addonBuilder(manifest);

builder.defineCatalogHandler(({ type, id }) => {
  return Promise.resolve({ metas: [
    { id: 'tt1234567', type: 'movie', name: 'Example Movie', poster: '...' }
  ]});
});

builder.defineStreamHandler(({ type, id }) => {
  return Promise.resolve({ streams: [
    { title: 'Stream 1', url: 'https://...' }
  ]});
});

module.exports = builder.getInterface();
```

### Testing en Stremio Web

1. Levantar addon en `http://localhost:7000`
2. En Stremio Web: `#/addons`
3. Agregar URL: `http://localhost:7000/manifest.json`
4. El addon aparece en los catalogos

## Tipos de Contenido

| Type | Descripcion |
|------|-------------|
| `movie` | Peliculas |
| `series` | Series de TV |
| `channel` | Canales de TV |
| `tv` | TV en vivo |
| `anime` | Anime |
| `other` | Otros |

## Extra Parameters

Los catalogs pueden aceptar parametros extra:

| Extra | Proposito |
|-------|-----------|
| `search` | Busqueda por texto |
| `genre` | Filtrar por genero |
| `skip` | Paginacion (offset) |
| `sort` | Ordenamiento |
