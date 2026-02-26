# Indie Studio Toolkit v4.0

**Claude Code + Cursor IDE Plugin** — Tres skills especializados en un plugin: SaaS Orchestrator + Hugo Expert + Stremio Web Expert.

Compatible con **Claude Code** y **Cursor IDE**.

---

## Skill 1: Indie Studio Orchestrator v2.0

Director Tecnico Orquestador para estudios indie (1-5 personas + agentes AI) construyendo B2B SaaS.

### Bucle de Orquestacion Multi-Agente

```
Arquitecto    ->  Apruebas el plan?
Backend       ->  Apruebas la API?
Frontend      ->  Apruebas la UI?
QA & Release  ->  Listo para commit
```

### Comandos Roadmap

| Comando | Que hace |
|---------|----------|
| `/roadmap-init` | Bootstrap: escanea codebase, genera `.indie-studio/` |
| `/roadmap-status` | Dashboard: estado actual + desviaciones |
| `/roadmap-sprint` | Planificar sprint con velocity y 20% deuda |
| `/roadmap-release` | Changelog + checklist staging + deploy |
| `/roadmap-audit` | Auditoria completa (6 dimensiones, score /30) |
| `/roadmap-pivot` | Recalcular prioridades |
| `/roadmap-retro` | Retrospectiva: metricas + lecciones |
| `/roadmap-sync` | Sincronizar `.indie-studio/` vs codebase |
| `/roadmap-delegate` | Generar prompts de delegacion por agente |

---

## Skill 2: Hugo Expert

Senior Hugo Developer + Site Architect. Experto completo en el framework Hugo (Go).

### Comandos Hugo

| Comando | Que hace |
|---------|----------|
| `/hugo-init` | Bootstrap sitio Hugo, genera `.hugo-expert/` |
| `/hugo-template` | Crea o debuggea templates (lookup order verification) |
| `/hugo-content` | Organiza contenido: bundles, taxonomias, archetypes, i18n |
| `/hugo-pipes` | Configura asset pipeline: Tailwind, SCSS, ESBuild, imagenes |
| `/hugo-deploy` | Prepara deployment: Netlify, Vercel, CF Pages, GH Pages, VPS |
| `/hugo-audit` | Auditoria completa: 8 dimensiones, score /40 |

---

## Skill 3: Stremio Web Expert (NEW)

Senior Stremio Web Developer. Especializado en la arquitectura unica de stremio-web: React 18 SPA + Rust/WASM core.

### Tres sombreros

- **WASM Bridge Architect** — Core WASM, useModelState, CoreSuspender, dispatch patterns
- **UI Component Engineer** — 32+ componentes, Less styling, spatial navigation, PWA
- **Addon & Streaming Specialist** — Addon protocol, player, Chromecast, streaming server

### Comandos Stremio

| Comando | Que hace |
|---------|----------|
| `/stremio-init` | Analiza codebase, genera `.stremio-expert/` |
| `/stremio-component` | Crea o modifica componentes (Less, props, spatial nav) |
| `/stremio-route` | Crea o modifica rutas (hash router, CoreSuspender) |
| `/stremio-addon` | Scaffold, debug, o integrar addons |
| `/stremio-audit` | Auditoria completa: 8 dimensiones, score /40 |
| `/stremio-debug` | Debug WASM bridge, routing, player, streaming, build |

### 8 Guardrails Stremio

1. **WASM Core = Truth** — Estado de negocio solo en el core Rust
2. **Custom Hash Router** — No React Router, jamas
3. **Less Only** — No CSS-in-JS, no Tailwind, no styled-components
4. **Reuse Components** — 32 componentes base antes de crear nuevos
5. **Worker Thread** — WASM en Web Worker, main thread libre
6. **Spatial Navigation** — tabIndex en todo, Smart TV compatible
7. **PWA Compliant** — Service worker, offline, manifest
8. **i18n Mandatory** — t('KEY') para toda string visible

### 8 Reference Documents

| Reference | Contenido |
|-----------|-----------|
| `wasm-core-bridge.md` | Core WASM, dispatch, useModelState, CoreSuspender, modelos |
| `react-components.md` | Sistema de componentes, patrones, Less, colores, imports |
| `custom-router.md` | Hash router, routesRegexp, navegacion, crear rutas |
| `addon-system.md` | Protocolo, manifest, transport URLs, crear addons |
| `player-streaming.md` | Player, streaming server, Chromecast, subtitulos |
| `build-tooling.md` | Webpack 5, loaders, Docker, CI/CD, env vars |
| `pwa-platform.md` | PWA, platform detection, Shell, iOS, spatial nav |
| `testing-i18n.md` | Jest, translation scan, ESLint, testing patterns |

---

## Installation

### Claude Code

```bash
/plugin marketplace add onick/indie-studio-roadmap
```

Then run `/plugin menu` to install. Restart Claude Code after.

### Cursor IDE

```bash
cp -r skills/ /path/to/your-project/.cursor/skills/
cp -r rules/ /path/to/your-project/.cursor/rules/
cp -r commands/ /path/to/your-project/.cursor/commands/
```

Restart Cursor IDE.

---

## Repository Structure

```
indie-studio-roadmap/
├── .claude-plugin/                           # Claude Code metadata
├── .claude/                                  # Claude Code
│   ├── commands/
│   │   ├── roadmap-*.md                      #   9 roadmap commands
│   │   ├── hugo-*.md                         #   6 hugo commands
│   │   └── stremio-*.md                      #   6 stremio commands
│   └── skills/
│       ├── indie-studio-roadmap/             #   SaaS Orchestrator
│       ├── hugo-expert/                      #   Hugo Expert
│       └── stremio-expert/                   #   Stremio Expert
│
├── skills/                                   # Cursor: skills
├── commands/                                 # Cursor: commands
├── rules/                                    # Cursor: rules (.mdc)
│   ├── hugo/                                 #   8 Hugo guardrails
│   └── stremio/                              #   6 Stremio guardrails
│
├── LICENSE
└── README.md
```

## Changelog

### v4.0 (2026-02-26)
- **Stremio Web Expert** — New skill: senior Stremio developer with 6 commands, 8 references, 8 guardrails
- Full React+WASM architecture knowledge, addon system, player, PWA, spatial navigation

### v3.0 (2026-02-26)
- **Hugo Expert** — Senior Hugo developer with 6 commands, 8 references, 8 guardrails
- Cursor IDE compatibility

### v2.0 (2026-02-26)
- Multi-Agent Orchestration Loop (4 phases with human approval)

### v1.0 (2026-02-25)
- Initial release: roadmap, sprints, debt, metrics

## License

MIT

---

Creado por [Contan2](https://contan2.com)
