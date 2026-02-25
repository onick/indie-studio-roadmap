# 🗺️ Indie Studio Roadmap Orchestrator

**Claude Code Plugin** — Director Técnico, Product Architect y Release Captain para estudios indie (1-5 personas + agentes AI) construyendo productos B2B SaaS.

## Qué hace

Gestiona roadmap, sprints, releases y deuda técnica entre sesiones de Claude Code. Mantiene estado persistente en `.indie-studio/` dentro de tu proyecto.

### Tres roles, una cabeza

- **Director Técnico:** Prioridades técnicas. Deuda trackeada, no ignorada.
- **Product Architect:** Módulos con dependencias claras. Un módulo terminado > tres empezados.
- **Release Captain:** Staging → QA → tag → producción. Sin excepciones.

## Installation

```
/plugin marketplace add onick/indie-studio-roadmap
```

Then run `/plugin menu` to install. Restart Claude Code after.

## Comandos

| Comando | Qué hace |
|---------|----------|
| `/roadmap-init` | Bootstrap: escanea codebase, genera `.indie-studio/` |
| `/roadmap-status` | Dashboard: estado actual + desviaciones detectadas |
| `/roadmap-sprint` | Planificar próximo sprint con velocity y 20% deuda |
| `/roadmap-release` | Changelog + checklist staging + comandos deploy |
| `/roadmap-audit` | Auditoría completa de salud (6 dimensiones, score /30) |
| `/roadmap-pivot` | Recalcular prioridades cuando la realidad cambia |
| `/roadmap-retro` | Retrospectiva: métricas + lecciones + patrones |
| `/roadmap-sync` | Sincronizar `.indie-studio/` vs codebase real |

## Estado Persistente

Al ejecutar `/roadmap-init`, se genera `.indie-studio/` en tu proyecto:

```
.indie-studio/
├── roadmap.md          # Visión, fases, dependencias
├── sprint-current.md   # Sprint activo con tareas
├── sprint-log.md       # Historial de sprints y retros
├── modules.md          # Registro de módulos y estado
├── releases.md         # Historial de releases
├── debt-register.md    # Deuda técnica con severidad
└── metrics.md          # Velocity y métricas entre sesiones
```

## Principios

1. **Un módulo terminado vale más que tres empezados.** Terminado = endpoint + frontend + test + staging.
2. **La deuda técnica es inventario.** Se trackea en `debt-register.md` con severidad y deadline. 20% de cada sprint va a deuda.
3. **Release es un ritual, no un accidente.** Staging → checklist → tag → producción. Siempre.
4. **Los agentes AI son junior devs.** Necesitan scope preciso, no "construye el módulo X".
5. **Mide lo que importa.** Pre-launch: módulos completados, deploy frequency. Post-launch: MRR, churn, TTFV.
6. **El roadmap es un documento vivo.** Se actualiza cada sprint. Pivotar no es vergüenza.

## Stack soportado

- React/TS frontend, API backend (Laravel/Node/Python)
- Docker, VPS o cloud simple
- Equipos de 1-5 personas + agentes AI
- Productos B2B SaaS

## Estructura

```
indie-studio-roadmap/
├── .claude-plugin/
│   ├── plugin.json                       # Plugin metadata
│   └── marketplace.json                  # Marketplace registry
├── .claude/
│   ├── commands/
│   │   ├── roadmap-init.md
│   │   ├── roadmap-status.md
│   │   ├── roadmap-sprint.md
│   │   ├── roadmap-release.md
│   │   ├── roadmap-audit.md
│   │   ├── roadmap-pivot.md
│   │   ├── roadmap-retro.md
│   │   └── roadmap-sync.md
│   └── skills/
│       └── indie-studio-roadmap/
│           ├── SKILL.md                  # Cerebro principal
│           └── references/
│               ├── product-strategy.md
│               ├── release-management.md
│               ├── tech-debt-patterns.md
│               ├── ai-native-workflows.md
│               ├── metrics-and-kpis.md
│               ├── infrastructure-checklist.md
│               ├── saas-launch-checklist.md
│               └── module-decomposition.md
├── LICENSE
└── README.md
```

## Idioma

- **Comunicación:** Español
- **Código, commits, variables:** Inglés

## License

MIT

---

Creado por [Contan2](https://contan2.com) — Building SilexPMS 🏨
