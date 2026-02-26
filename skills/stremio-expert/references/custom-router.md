# Custom Hash Router — Reference

## Arquitectura del Router

Stremio Web usa un router custom basado en `window.location.hash`. NO usa React Router.

## Componentes del Router

### routesRegexp.js

**Ubicacion:** `src/common/routesRegexp.js`

Define patrones regex para todas las rutas:

```javascript
module.exports = {
    intro: /^\/intro$/,
    board: /^\/$|^\/board$/,
    discover: /^\/discover(?:\/([^/]+))?(?:\/([^/]+))?(?:\/([^/]+))?$/,
    library: /^\/library(?:\/([^/]+))?$/,
    calendar: /^\/calendar(?:\/(\d+))?(?:\/(\d+))?$/,
    search: /^\/search$/,
    metadetails: /^\/(?:metadetails|detail)\/([^/]+)\/([^/]+)(?:\/([^/]+))?$/,
    addons: /^\/addons(?:\/([^/]+))?(?:\/([^/]+))?(?:\/([^/]+))?$/,
    settings: /^\/settings$/,
    player: /^\/player\/([^/]+)(?:\/([^/]+))?(?:\/([^/]+))?(?:\/([^/]+))?(?:\/([^/]+))?(?:\/([^/]+))?$/
};
```

### Router.js

**Ubicacion:** `src/router/Router/Router.js`

```javascript
// Simplificado
function Router() {
    const [route, setRoute] = React.useState(() => matchRoute(window.location.hash));

    React.useEffect(() => {
        const onHashChange = () => {
            const newRoute = matchRoute(window.location.hash);
            setRoute(prevRoute => {
                // Deep equality check — no re-render si la ruta es igual
                if (deepEqual(prevRoute, newRoute)) return prevRoute;
                return newRoute;
            });
        };

        window.addEventListener('hashchange', onHashChange);
        return () => window.removeEventListener('hashchange', onHashChange);
    }, []);

    // Renderiza la ruta matched
    const RouteComponent = routeComponents[route.name] || NotFound;
    return (
        <RouteFocusedProvider>
            <RouteComponent urlParams={route.params} queryParams={route.query} />
        </RouteFocusedProvider>
    );
}
```

### matchRoute

```javascript
function matchRoute(hash) {
    // Quita el '#' inicial
    const path = hash.slice(1) || '/';

    // Busca match en routesRegexp
    for (const [name, regexp] of Object.entries(routesRegexp)) {
        const match = path.match(regexp);
        if (match) {
            return {
                name,
                params: match.slice(1), // Capture groups
                query: parseQueryString(path)
            };
        }
    }

    return { name: 'notfound', params: [], query: {} };
}
```

## Navegacion

### Navegar programaticamente

```javascript
// Hash navigation — la forma estandar en Stremio
window.location.hash = '#/discover/movie/top';
window.location.hash = '#/metadetails/movie/tt1234567';
window.location.hash = '#/player/' + encodeURIComponent(streamUrl);

// Volver atras
window.history.back();

// Reemplazar (sin agregar al historial)
window.location.replace('#/board');
```

### Links en JSX

```javascript
// Usa <a> con href hash
<a href="#/discover/movie/top">Discover Movies</a>
<a href={`#/metadetails/${type}/${id}`}>Details</a>

// O Button con onClick
<Button onClick={() => { window.location.hash = '#/settings'; }} />
```

## Crear una Nueva Ruta

### Paso 1: Agregar regex

```javascript
// src/common/routesRegexp.js
module.exports = {
    // ... rutas existentes
    myNewRoute: /^\/my-new-route(?:\/([^/]+))?$/,
};
```

### Paso 2: Crear componente de ruta

```
src/routes/MyNewRoute/
├── MyNewRoute.js
├── styles.less
└── index.js
```

```javascript
// MyNewRoute.js
const React = require('react');
const classnames = require('classnames');
const { useModelState } = require('stremio/common');
const { MainNavBars } = require('stremio/components');
const styles = require('./styles.less');

const MyNewRoute = ({ urlParams, queryParams }) => {
    const [param1] = urlParams;

    return (
        <MainNavBars className={styles['my-new-route-container']} route={'myNewRoute'}>
            <div className={styles['content']}>
                {/* contenido */}
            </div>
        </MainNavBars>
    );
};

module.exports = MyNewRoute;
```

### Paso 3: Registrar en routes/index.js

```javascript
// src/routes/index.js
const MyNewRoute = require('./MyNewRoute');

module.exports = {
    // ... rutas existentes
    myNewRoute: MyNewRoute,
};
```

### Paso 4: Agregar al MainNavBars (si necesita entry en sidebar)

Actualizar la configuracion de navegacion para incluir la nueva ruta.

## Route Parameters

### URL Params (posicionales)

```javascript
// Para /metadetails/:type/:id/:videoId?
// URL: #/metadetails/movie/tt1234567/s01e01
const [type, id, videoId] = urlParams;
// type = 'movie', id = 'tt1234567', videoId = 's01e01'
```

### Query Params

```javascript
// URL: #/discover/movie/top?sort=year&genre=action
// queryParams = { sort: 'year', genre: 'action' }
```

## Protected Routes

El router incluye proteccion para rutas que requieren autenticacion:

```javascript
// RouterWithProtectedRoutes verifica si el usuario esta autenticado
// Si no, redirige a /intro
function RouterWithProtectedRoutes() {
    const profile = useProfile();

    if (!profile.auth && route.protected) {
        window.location.hash = '#/intro';
        return null;
    }

    return <Router />;
}
```

## RouteFocusedProvider

Cada ruta se envuelve en `RouteFocusedProvider` que gestiona el focus para spatial navigation:

```javascript
// Cuando la ruta cambia, el focus se resetea al contenedor principal
// Esto es critico para navegacion con control remoto en Smart TVs
```
