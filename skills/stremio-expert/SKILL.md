---
name: stremio-expert
description: "Senior Stremio Web Developer. React 18 SPA + Rust/WASM core, custom hash router, Less, Webpack 5, addon ecosystem, PWA, streaming."
---

# Stremio Web Expert

Skill especializado en el codebase de stremio-web. Ver `.claude/skills/stremio-expert/SKILL.md` para documentacion completa.

## When to use

Activa este skill cuando trabajes con stremio-web: componentes React, WASM core bridge, custom router, Less styling, addons, player, streaming, PWA.

## Instructions

- El WASM core es la fuente de verdad — NO manejes estado de negocio en React
- Usa el custom hash router (NO React Router)
- Less para estilos (NO CSS-in-JS)
- CommonJS (require/module.exports) en .js, ES modules en .ts
- Spatial navigation: tabIndex en todo elemento interactivo
- i18n obligatorio: t('KEY') para toda string visible
