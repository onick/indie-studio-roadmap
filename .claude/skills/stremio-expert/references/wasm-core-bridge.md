# WASM Core Bridge — Reference

## Arquitectura General

Stremio Web tiene una arquitectura unica: la logica de negocio vive en Rust, compilada a WebAssembly, y React es solo la capa visual.

```
stremio-core (Rust) → stremio-core-web (WASM) → Core Service (JS) → React Hooks → UI
```

## stremio-core-web

Paquete npm: `@stremio/stremio-core-web`

Expone:
- `CoreTransport` — la clase principal para comunicarse con el core
- `worker.js` — Web Worker que ejecuta el WASM module

El worker se registra como entry point separado en Webpack:

```javascript
// webpack.config.js
entry: {
  main: './src/index.js',
  worker: '@stremio/stremio-core-web/worker.js'
}
```

## Core Service

**Ubicacion:** `src/services/Core/Core.js`

El Core Service es el bridge entre React y el WASM core. Es un EventEmitter3.

### Inicializacion

```javascript
// Simplificado
const core = new Core();
core.transport = new CoreTransport({
  // URL del streaming server local
  streamingServerUrl: 'http://127.0.0.1:11470/',
  // URL del API de Stremio
  apiUrl: 'https://api.strem.io',
});
```

### Dispatch (enviar acciones al core)

```javascript
// Patron: dispatch(action, model)
core.transport.dispatch({
  action: 'Load',
  args: {
    model: 'CatalogsWithExtra',
    args: { type: 'movie' }
  }
});

// O con el action como objeto completo
core.transport.dispatch({
  action: 'Ctx',
  args: {
    action: 'Authenticate',
    args: { type: 'Login', email, password }
  }
});
```

### Recibir estado

```javascript
// El core emite 'NewState' cuando el estado cambia
core.on('NewState', (state) => {
  // state contiene el modelo actualizado
  console.log(state);
});
```

### Modelos disponibles del Core

| Modelo | Proposito |
|--------|-----------|
| `CatalogsWithExtra` | Catalogos de addons con filtros |
| `LibraryWithFilters` | Biblioteca del usuario con filtros |
| `MetaDetails` | Detalles de un meta item (pelicula/serie) |
| `Player` | Estado del player (stream, subtitles, etc.) |
| `Addons` | Lista de addons instalados/disponibles |
| `InstalledAddons` | Solo addons instalados |
| `StreamingServer` | Estado del streaming server |
| `Ctx` | Contexto global (auth, settings, profile) |
| `Notifications` | Notificaciones de contenido |
| `Search` | Resultados de busqueda |
| `Calendar` | Calendario de releases |

## useModelState Hook

**Ubicacion:** `src/common/useModelState.js`

Hook principal para conectar componentes React al estado WASM.

```javascript
import { useModelState } from 'stremio/common';

function DiscoverPage() {
  const catalogs = useModelState({
    model: 'CatalogsWithExtra',
    action: 'Load',
    args: { type: 'movie', catalogId: 'top' }
  });

  // catalogs contiene el estado del modelo
  // Se actualiza automaticamente cuando el core emite NewState
  // Deep equality check evita re-renders innecesarios

  return (
    <div>
      {catalogs.map(catalog => (
        <MetaRow key={catalog.id} items={catalog.items} />
      ))}
    </div>
  );
}
```

### Como funciona internamente

1. En mount: despacha `action` al core con `args`
2. Escucha eventos `NewState` del core
3. Compara nuevo estado con anterior usando `fast-equals.deepEqual`
4. Solo re-renderiza si el estado cambio estructuralmente
5. En unmount: limpia listeners

## CoreSuspender

**Ubicacion:** `src/common/CoreSuspender.js`

Integra el core WASM con React Suspense.

```javascript
import { CoreSuspender } from 'stremio/common';

function MyRoute() {
  return (
    <React.Suspense fallback={<Loading />}>
      <CoreSuspender>
        <ActualContent />
      </CoreSuspender>
    </React.Suspense>
  );
}
```

### Mecanismo

- Mientras el core no ha respondido con el estado inicial, `CoreSuspender` lanza una Promise (el patron de React Suspense)
- React muestra el fallback automaticamente
- Cuando el core responde, la Promise se resuelve y React renderiza el contenido

## Patrones de Dispatch

### Autenticacion

```javascript
// Login
core.transport.dispatch({
  action: 'Ctx',
  args: { action: 'Authenticate', args: { type: 'Login', email, password } }
});

// Logout
core.transport.dispatch({
  action: 'Ctx',
  args: { action: 'Logout' }
});
```

### Library

```javascript
// Cargar library
core.transport.dispatch({
  action: 'Load',
  args: { model: 'LibraryWithFilters', args: { type: 'movie' } }
});

// Agregar a library
core.transport.dispatch({
  action: 'Ctx',
  args: { action: 'AddToLibrary', args: { metaItem } }
});
```

### Addons

```javascript
// Instalar addon
core.transport.dispatch({
  action: 'Ctx',
  args: { action: 'InstallAddon', args: { transportUrl: 'https://...' } }
});

// Desinstalar addon
core.transport.dispatch({
  action: 'Ctx',
  args: { action: 'UninstallAddon', args: { transportUrl: 'https://...' } }
});
```

### Settings

```javascript
// Actualizar setting
core.transport.dispatch({
  action: 'Ctx',
  args: { action: 'UpdateSettings', args: { interfaceLanguage: 'es' } }
});
```

## Hooks Derivados del Core

| Hook | Fuente | Proposito |
|------|--------|-----------|
| `useProfile` | `Ctx` | Datos del usuario (auth, email, avatar) |
| `useSettings` | `Ctx` | Configuracion de la app |
| `useStreamingServer` | `StreamingServer` | Estado del server local |
| `useNotifications` | `Notifications` | Alertas de nuevo contenido |
| `useTorrent` | `StreamingServer` | Estado de descargas torrent |

## Reglas del Bridge

1. **Nunca** almacenes estado de negocio en `useState` o `useReducer`
2. **Nunca** hagas fetch HTTP directamente para datos de catalogo/stream — eso pasa por el core
3. **Siempre** usa `useModelState` para leer estado del core
4. **Siempre** usa `core.transport.dispatch()` para acciones
5. El core es **asincrono** — los eventos `NewState` llegan cuando el core procesa
6. El core corre en un **Web Worker** — no bloquea el main thread
