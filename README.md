# 🗺️ Indie Studio Orchestrator v2.0

**Claude Code Plugin** — Director Técnico Orquestador para estudios indie (1-5 personas + agentes AI) construyendo B2B SaaS.

## Qué hace

Orquesta el ciclo completo de desarrollo: **Arquitecto → Backend → Frontend → QA**, pidiendo aprobación humana en cada paso. Mantiene estado persistente en `.indie-studio/` entre sesiones.

### v2.0: Bucle de Orquestación Multi-Agente

```
🧠 Arquitecto    →  ⏸️ ¿Apruebas el plan?
👨‍💻 Backend       →  ⏸️ ¿Apruebas la API?
⚛️ Frontend      →  ⏸️ ¿Apruebas la UI?
🧪 QA & Release  →  ✅ Listo para commit
```

El agente NUNCA avanza al siguiente paso sin tu aprobación. Tú eres El Piloto.

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
├── sprint-current.md   # Sprint activo con tareas + fase del bucle
├── sprint-log.md       # Historial de sprints y retros
├── modules.md          # Registro de módulos y estado
├── releases.md         # Historial de releases
├── debt-register.md    # Deuda técnica con severidad
└── metrics.md          # Velocity, release cadence, y approval rate
```

## Guardrails

1. **Humano en el Medio** — Nunca avanza sin aprobación explícita. CADA paso requiere tu OK.
2. **Un módulo terminado > tres empezados** — Endpoint + frontend + test + staging.
3. **Deuda es inventario** — 20% de cada sprint. No negociable.
4. **Release es ritual** — Staging → checklist → tag → producción.
5. **Estado primero, UI después** — El backend es la verdad. El frontend es vista.
6. **Agentes AI son juniors** — Scope preciso, no "construye el módulo X".
7. **Testing obligatorio** — Flujos de dinero y auth sin tests = rechazados.
8. **Roadmap vivo** — Se actualiza cada sprint. Pivotar no es vergüenza.

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
│   ├── commands/                         # 8 slash commands
│   └── skills/
│       └── indie-studio-roadmap/
│           ├── SKILL.md                  # Cerebro v2.0
│           └── references/               # 8 knowledge docs
├── LICENSE
└── README.md
```

## Changelog

### v2.0 (2026-02-26)
- **Bucle de Orquestación Multi-Agente** — 4 fases con aprobación humana obligatoria
- **Guardrail G1: Humano en el Medio** — Nunca avanza sin OK explícito
- **Tracking de fase en sprint** — Cada tarea sabe en qué paso del bucle está
- **Métricas de aprobación** — Approval rate y rechazos del piloto
- **Sprint log mejorado** — Registra rechazos como aprendizaje

### v1.0 (2026-02-25)
- Release inicial: roadmap, sprints, deuda, métricas, 8 commands, 8 references

## License

MIT

---

Creado por [Contan2](https://contan2.com) — Building SilexPMS 🏨
