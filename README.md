# 🗺️ Indie Studio Roadmap Orchestrator

**Claude Code Skill** — Director Técnico, Product Architect y Release Captain para estudios indie (1-5 personas + agentes AI) construyendo productos B2B SaaS.

## Qué hace

Gestiona roadmap, sprints, releases y deuda técnica entre sesiones de Claude Code. Mantiene estado persistente en `.indie-studio/` dentro de tu proyecto.

### Tres roles, una cabeza

- **Director Técnico:** Prioridades técnicas. Deuda trackeada, no ignorada.
- **Product Architect:** Módulos con dependencias claras. Un módulo terminado > tres empezados.
- **Release Captain:** Staging → QA → tag → producción. Sin excepciones.

## Instalación

### Opción 1: Clonar e instalar

```bash
# Clonar
git clone https://github.com/onick/indie-studio-roadmap.git ~/.agents/skills/indie-studio-roadmap

# Symlink del skill
ln -s ../../.agents/skills/indie-studio-roadmap ~/.claude/skills/indie-studio-roadmap

# Copiar commands (slash commands)
cp -r ~/.agents/skills/indie-studio-roadmap/.claude/commands/roadmap-*.md ~/.claude/commands/
```

### Opción 2: Manual

Copia los archivos a sus ubicaciones:
- `SKILL.md` + `references/` → `~/.agents/skills/indie-studio-roadmap/`
- `.claude/commands/roadmap-*.md` → `~/.claude/commands/`
- Symlink: `~/.claude/skills/indie-studio-roadmap` → skill directory

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

## Estructura del Skill

```
indie-studio-roadmap/
├── SKILL.md                              # Cerebro principal
├── README.md                             # Este archivo
├── references/
│   ├── product-strategy.md               # Estrategia B2B SaaS
│   ├── release-management.md             # Git flow, Docker, staging-first
│   ├── tech-debt-patterns.md             # Patrones de deuda técnica
│   ├── ai-native-workflows.md            # Coordinación humanos + AI
│   ├── metrics-and-kpis.md               # Métricas pre/post-launch
│   ├── infrastructure-checklist.md       # VPS, Docker, backups
│   ├── saas-launch-checklist.md          # Checklist para lanzar SaaS
│   └── module-decomposition.md           # Descomponer en módulos
└── .claude/
    └── commands/
        ├── roadmap-init.md
        ├── roadmap-status.md
        ├── roadmap-sprint.md
        ├── roadmap-release.md
        ├── roadmap-audit.md
        ├── roadmap-pivot.md
        ├── roadmap-retro.md
        └── roadmap-sync.md
```

## Idioma

- **Comunicación:** Español
- **Código, commits, variables:** Inglés

## Licencia

MIT

---

Creado por [Contan2](https://contan2.com) — Building SilexPMS 🏨
