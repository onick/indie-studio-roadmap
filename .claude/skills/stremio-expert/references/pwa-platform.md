# PWA & Platform — Reference

## Progressive Web App

Stremio Web funciona como PWA con soporte offline, instalacion, y platform-specific behavior.

### Service Worker

Generado por Workbox (WorkboxPlugin.GenerateSW):

```javascript
// webpack.config.js
new WorkboxPlugin.GenerateSW({
  clientsClaim: true,
  skipWaiting: true,
  // Precache de assets del build
  // Runtime caching para API calls
})
```

Se puede deshabilitar con `SERVICE_WORKER_DISABLED=true`.

### Registro del SW

```javascript
// src/index.js (simplificado)
if ('serviceWorker' in navigator && !process.env.SERVICE_WORKER_DISABLED) {
  window.addEventListener('load', () => {
    navigator.serviceWorker.register('/sw.js');
  });
}
```

### Web App Manifest

Define como se ve la app cuando se instala:
- Nombre y descripcion
- Iconos (multiples resoluciones)
- Theme color y background color
- Display mode: `standalone`
- Start URL

### Hook usePWA

```javascript
const { usePWA } = require('stremio/common');

function InstallBanner() {
    const { isInstalled, isStandalone, promptInstall } = usePWA();

    if (isInstalled || isStandalone) return null;

    return (
        <Button label="Install App" onClick={promptInstall} />
    );
}
```

## Platform Detection

### Bowser

```javascript
const Bowser = require('bowser');
const browser = Bowser.getParser(navigator.userAgent);

// Deteccion
const isIOS = browser.getOSName() === 'iOS';
const isAndroid = browser.getOSName() === 'Android';
const isMobile = browser.getPlatformType() === 'mobile';
const isDesktop = browser.getPlatformType() === 'desktop';
const isTV = browser.getPlatformType() === 'tv';
```

### En el bootstrap

```javascript
// src/index.js
// En desktop, quita el viewport meta tag (no necesita responsive scaling)
if (browser.getPlatformType() === 'desktop') {
    const viewport = document.querySelector('meta[name=viewport]');
    if (viewport) viewport.remove();
}
```

## PlatformProvider

Provee contexto de plataforma a toda la app:

```javascript
// src/App/PlatformProvider
// Detecta:
// - Platform type (web, pwa, embedded)
// - OS (iOS, Android, Windows, macOS, Linux)
// - Browser capabilities
// - Streaming server availability
// - Chromecast availability
```

## Shell Service

**Ubicacion:** `src/services/Shell/`

Bridge para integracion con la plataforma nativa:

```javascript
// Funcionalidades del Shell:
shell.openExternal(url);        // Abrir URL en browser
shell.setTitle(title);          // Cambiar titulo de ventana
shell.setFullscreen(bool);      // Toggle fullscreen
shell.minimizeWindow();         // Minimizar
shell.maximizeWindow();         // Maximizar
shell.closeWindow();            // Cerrar
```

### Hook useShell

```javascript
const { useShell } = require('stremio/common');

function ExternalLink({ url, children }) {
    const shell = useShell();

    return (
        <a onClick={() => shell.openExternal(url)}>
            {children}
        </a>
    );
}
```

## iOS PWA Specifics

Trabajo reciente en el repo (Feb 2026):

### Safe Areas

```less
// Para iOS notch y home indicator
.container {
    padding-top: env(safe-area-inset-top);
    padding-bottom: env(safe-area-inset-bottom);
    padding-left: env(safe-area-inset-left);
    padding-right: env(safe-area-inset-right);
}
```

### Status Bar

```html
<!-- En index.html -->
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-capable" content="yes">
```

### Theme Color

```html
<meta name="theme-color" content="var(--background-color)">
```

## Spatial Navigation (Smart TVs)

### spatial-navigation-polyfill

Polyfill que permite navegacion con D-pad (flechas, control remoto):

```javascript
// Los elementos focusables necesitan:
// 1. tabIndex={0} o ser naturalmente focusable
// 2. Estar visibles en el viewport
// 3. Tener dimensiones > 0

<div tabIndex={0} className={styles['card']}>
    {/* El polyfill mueve el focus con las flechas */}
</div>
```

### Patrones de focus management

```javascript
// Auto-focus al montar
React.useEffect(() => {
    ref.current?.focus();
}, []);

// Focus trap en modales
const { FocusLock } = require('react-focus-lock');
<FocusLock>
    <ModalDialog />
</FocusLock>

// Restore focus al cerrar
const previousFocus = React.useRef(document.activeElement);
React.useEffect(() => {
    return () => previousFocus.current?.focus();
}, []);
```

## Fullscreen

### Hook useFullscreen

```javascript
const { useFullscreen } = require('stremio/common');

function Player() {
    const { isFullscreen, toggleFullscreen, requestFullscreen, exitFullscreen } = useFullscreen();

    return (
        <Button
            icon={isFullscreen ? 'minimize' : 'maximize'}
            onClick={toggleFullscreen}
        />
    );
}
```

## Orientation

### Hook useOrientation

```javascript
const { useOrientation } = require('stremio/common');

function Player() {
    const { orientation, lockOrientation } = useOrientation();

    React.useEffect(() => {
        // Lock landscape en player
        lockOrientation('landscape');
        return () => lockOrientation('natural');
    }, []);
}
```

## Platform-specific Behavior Matrix

| Feature | Web | PWA iOS | PWA Android | Smart TV |
|---------|-----|---------|-------------|----------|
| Streaming server | ✅ | ⚠️ Limited | ⚠️ Limited | ❌ |
| Chromecast | ✅ | ✅ | ✅ | ❌ |
| External player | ✅ | ❌ | ✅ (intent) | ❌ |
| File drop | ✅ | ❌ | ❌ | ❌ |
| Spatial nav | ✅ | N/A | N/A | ✅ Required |
| Install prompt | ✅ | N/A (Safari) | ✅ | N/A |
| Safe areas | N/A | ✅ Required | ✅ | N/A |
| Keyboard shortcuts | ✅ | Limited | Limited | Via remote |
