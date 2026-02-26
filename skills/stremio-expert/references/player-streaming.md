# Player & Streaming — Reference

## Arquitectura del Player

El Player es la ruta mas compleja de Stremio Web. Combina:
- Reproductor de video HTML5
- Streaming server local (torrents → HTTP)
- Chromecast
- Subtitulos (multiples fuentes)
- Player externos

## Player Route

**Path:** `#/player/:stream/:streamTransportUrl?/:metaTransportUrl?/:type?/:id?/:videoId?`

- `:stream` — Stream encoded (base64 o URL encoded)
- `:streamTransportUrl` — URL del addon que provee el stream
- `:metaTransportUrl` — URL del addon que provee metadata
- `:type` — Tipo de contenido (movie/series)
- `:id` — ID del meta item
- `:videoId` — ID del video/episodio

## stremio-video

Paquete: `stremio-video`

Abstraccion del video player que soporta multiples backends:
- HTML5 Video (default)
- Chromecast receiver
- External player (VLC, mpv, etc.)

```javascript
// Eventos del player
player.on('timeUpdate', ({ time, duration }) => {});
player.on('stateChanged', ({ paused, buffering }) => {});
player.on('volumeChanged', ({ volume, muted }) => {});
player.on('subtitlesChanged', ({ track }) => {});
player.on('ended', () => {});
player.on('error', (error) => {});
```

## Streaming Server

El streaming server local corre en `http://127.0.0.1:11470/`.

### Proposito

Convierte fuentes de streaming (torrents, magnet links) a streams HTTP que el browser puede reproducir.

### Estado en React

```javascript
const streamingServer = useStreamingServer();
// {
//   baseUrl: 'http://127.0.0.1:11470/',
//   settings: { ... },
//   isAvailable: true/false,
//   statistics: { ... }
// }
```

### Verificar disponibilidad

```javascript
const { useStreamingServer } = require('stremio/common');

function PlayerStatus() {
    const server = useStreamingServer();

    if (!server.isAvailable) {
        return <Warning message="Streaming server not available" />;
    }

    return <Player />;
}
```

## Chromecast

**Servicio:** `src/services/Chromecast/`
**Receiver App ID:** `1634F54B`

### Inicializacion

El servicio Chromecast se inicializa automaticamente cuando el Google Cast SDK esta disponible (via `window.cast` y `window.chrome.cast`).

### Casting

```javascript
// El cast se maneja via el Core service
// Cuando un usuario selecciona un Chromecast:
core.transport.dispatch({
  action: 'Player',
  args: {
    action: 'CastTo',
    args: { deviceId: selectedDevice.id }
  }
});
```

### Cast State

```javascript
// El estado del cast viene del core
const playerState = useModelState({
  model: 'Player',
  action: 'Load',
  args: { ... }
});

// playerState incluye:
// - isCasting: boolean
// - castDevice: { id, name }
```

## Subtitulos

### Fuentes de subtitulos

1. **Addons** — OpenSubtitles y otros addons de subtitulos
2. **Embedded** — Subtitulos dentro del video (MKV, MP4)
3. **Local files** — Drag & drop de archivos .srt, .vtt, .ass
4. **URL** — Subtitulos remotos

### Seleccion de subtitulos

```javascript
// Via core dispatch
core.transport.dispatch({
  action: 'Player',
  args: {
    action: 'SelectSubtitle',
    args: { trackId: selectedTrack.id }
  }
});
```

### Drag & Drop de subtitulos

El servicio `DragAndDrop` detecta archivos de subtitulos dropeados:

```javascript
// src/services/DragAndDrop/
// Acepta: .srt, .vtt, .sub, .ass, .ssa
// Los convierte y los agrega al player
```

## Keyboard Shortcuts del Player

| Shortcut | Accion |
|----------|--------|
| `Space` / `K` | Play/Pause |
| `F` | Toggle fullscreen |
| `M` | Toggle mute |
| `←` | Seek -10s |
| `→` | Seek +10s |
| `↑` | Volume up |
| `↓` | Volume down |
| `S` | Toggle subtitles |
| `Escape` | Exit player |

### Implementacion

```javascript
// src/services/KeyboardShortcuts/
// Los shortcuts se registran globalmente
// El Player route agrega sus propios shortcuts via ShortcutsGroup
```

## Tipos de Stream

```javascript
// Un stream puede ser:
{
  // HTTP directo
  url: 'https://example.com/video.mp4',

  // Torrent/Magnet (necesita streaming server)
  infoHash: 'abc123...',
  fileIdx: 0,

  // YouTube
  ytId: 'dQw4w9WgXcQ',

  // External URL (abre en player externo)
  externalUrl: 'https://example.com/watch/123'
}
```

## Continue Watching

El estado de progreso se guarda automaticamente en la library del usuario:

```javascript
// El core trackea automaticamente:
// - Posicion actual (time)
// - Duracion total
// - Porcentaje visto
// - Ultimo episodio visto (series)

// Componente ContinueWatchingItem muestra el progreso
<ContinueWatchingItem
  libItem={item}
  progress={item.state.timeOffset / item.state.duration}
/>
```

## Player UI Structure

```
Player Route
├── VideoContainer
│   ├── stremio-video player
│   └── Subtitles overlay
├── Controls overlay
│   ├── TopBar (title, cast, settings)
│   ├── Timeline/Scrubber
│   ├── BottomBar (play, volume, fullscreen, subs)
│   └── SeekPreview
├── InfoPanel (metadata, next episode)
└── SettingsPanel (quality, audio, subtitles)
```
