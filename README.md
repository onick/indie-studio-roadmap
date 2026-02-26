# Indie Studio Toolkit v3.0

**Claude Code + Cursor IDE Plugin** — Dos skills especializados en un plugin: Orchestrator para B2B SaaS + Hugo Expert para sitios estaticos.

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

El agente NUNCA avanza sin tu aprobacion. Tu eres El Piloto.

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

### Tres sombreros

- **Arquitecto de Contenido** — Bundles, taxonomias, secciones, multilingual, archetypes
- **Template Engineer** — Go templates, partials, lookup order, render hooks, shortcodes
- **Asset Pipeline Specialist** — Hugo Pipes (SCSS, Tailwind, PostCSS, ESBuild, image processing)

### Comandos Hugo

| Comando | Que hace |
|---------|----------|
| `/hugo-init` | Bootstrap: escanea sitio Hugo o crea nuevo, genera `.hugo-expert/` |
| `/hugo-template` | Crea o debuggea templates (con lookup order verification) |
| `/hugo-content` | Organiza contenido: bundles, taxonomias, archetypes, i18n |
| `/hugo-pipes` | Configura asset pipeline: Tailwind, SCSS, ESBuild, imagenes |
| `/hugo-deploy` | Prepara deployment: Netlify, Vercel, CF Pages, GH Pages, VPS |
| `/hugo-audit` | Auditoria completa: 8 dimensiones, score /40 |

### 8 Guardrails Hugo

1. **Lookup Order First** — Verifica ANTES de crear templates
2. **Content Architecture First** — Estructura antes que templates
3. **Hugo Pipes Only** — No webpack/vite/gulp
4. **Page Bundles** — Contenido + recursos juntos
5. **Partial Cache** — `partialCached` para partials pesados
6. **Front Matter Cascade** — DRY con herencia
7. **Zero JS Default** — Cada KB debe justificarse
8. **i18n Day One** — Multilingue desde el inicio

### 8 Reference Documents

| Reference | Contenido |
|-----------|-----------|
| `hugo-templating.md` | Go templates, partials, lookup order, funciones, render hooks |
| `hugo-content-management.md` | Bundles, taxonomias, archetypes, front matter, multilingual, menus |
| `hugo-pipes-assets.md` | SCSS, Tailwind, PostCSS, ESBuild, image processing, fingerprinting |
| `hugo-modules-themes.md` | Hugo Modules, temas, mounts, vendoring |
| `hugo-shortcodes-hooks.md` | Shortcodes built-in y custom, render hooks (7 tipos) |
| `hugo-configuration.md` | hugo.toml completo, config directory, environments |
| `hugo-deployment.md` | Netlify, Vercel, CF Pages, GH Pages, VPS, CI/CD |
| `hugo-performance-seo.md` | Build optimization, SEO, OG, JSON-LD, Lighthouse |

---

## Installation

### Claude Code

```bash
/plugin marketplace add onick/indie-studio-roadmap
```

Then run `/plugin menu` to install. Restart Claude Code after.

### Cursor IDE

1. Clone or download this repo
2. Copy the Cursor directories into your project:

```bash
cp -r skills/ /path/to/your-project/.cursor/skills/
cp -r rules/ /path/to/your-project/.cursor/rules/
cp -r commands/ /path/to/your-project/.cursor/commands/
```

3. Restart Cursor IDE

---

## Repository Structure

```
indie-studio-roadmap/
├── .claude-plugin/                        # Claude Code metadata
│   ├── plugin.json
│   └── marketplace.json
├── .claude/                               # Claude Code
│   ├── commands/
│   │   ├── roadmap-*.md                   #   9 roadmap commands
│   │   └── hugo-*.md                      #   6 hugo commands
│   └── skills/
│       ├── indie-studio-roadmap/
│       │   ├── SKILL.md                   #   Orchestrator brain
│       │   └── references/                #   8 SaaS references
│       └── hugo-expert/
│           ├── SKILL.md                   #   Hugo brain
│           └── references/                #   8 Hugo references
│
├── skills/                                # Cursor: skills
│   └── hugo-expert/
│       ├── SKILL.md
│       └── references/
├── commands/                              # Cursor: commands
│   └── hugo/
│       └── hugo-*.md
├── rules/                                 # Cursor: rules (.mdc)
│   └── hugo/
│       ├── lookup-order-first.mdc
│       ├── content-before-templates.mdc
│       ├── hugo-pipes-only.mdc
│       ├── page-bundles.mdc
│       ├── partial-cache.mdc
│       ├── front-matter-cascade.mdc
│       ├── zero-js-default.mdc
│       └── i18n-from-day-one.mdc
│
├── LICENSE
└── README.md
```

## Changelog

### v3.0 (2026-02-26)
- **Hugo Expert skill** — Senior Hugo developer with 6 commands, 8 references, 8 guardrails
- **Cursor IDE compatibility** — Rules (.mdc), commands and skills with YAML frontmatter
- **Dual-platform plugin** — Works on both Claude Code and Cursor IDE

### v2.0 (2026-02-26)
- **Bucle de Orquestacion Multi-Agente** — 4 fases con aprobacion humana
- **Guardrail G1: Humano en el Medio** — Nunca avanza sin OK
- **Tracking de fase en sprint** — Cada tarea sabe en que paso del bucle esta
- **Metricas de aprobacion** — Approval rate y rechazos

### v1.0 (2026-02-25)
- Release inicial: roadmap, sprints, deuda, metricas, 8 commands, 8 references

## License

MIT

---

Creado por [Contan2](https://contan2.com)
