# Workflows AI-Native

## El Patron Sandwich

```
HUMANO decide QUE construir (estrategia, prioridad, scope)
    ↓
AGENTE AI construye el COMO (codigo, tests, deploy)
    ↓
HUMANO revisa el RESULTADO (funciona? alinea con el plan?)
```

El humano nunca abdica la estrategia. El agente nunca improvisa el scope.

## Como Scoping Tareas para Agentes AI

### Mal scope (demasiado amplio)
> "Construye el modulo de housekeeping"

El agente inventara estructura, endpoints, UI, permisos — todo desde su criterio. Resultado: 2000 lineas que no alinean con tu arquitectura.

### Buen scope (una unidad de trabajo)
> "Crea endpoint GET /api/housekeeping/rooms que retorne habitaciones agrupadas por piso. Cada habitacion incluye: id, number, room_type.name, housekeeping_status, occupancy_status. Scope por hotel_id del usuario autenticado. Test con PHPUnit. Usa API Resource, no model dump."

Caracteristicas de un buen scope:
- **Un endpoint** O **un componente** O **una migracion** — nunca todo junto.
- **Input/output claro:** que recibe, que retorna.
- **Restricciones explicitas:** que patron seguir, que evitar.
- **Verificacion:** como saber si esta bien.

## Prompts para Agentes por Rol

### Backend Agent (Laravel)
```
Actua como Senior Laravel Engineer. Objetivo: [TAREA].
Reglas: usar API Resources (no model dumps), evitar N+1,
DB::transaction en operaciones multi-tabla, idempotencia
en webhooks, scope siempre por hotel_id.
Entrega: controller + resource + test feature + notas de seguridad.
```

### Frontend Agent (React/TS)
```
Actua como Senior React Engineer. Arquitectura:
UI → Hooks (React Query) → Services → API layer.
TS estricto (sin any). Estados: loading/empty/error.
Entrega: componente + hook + mutaciones optimistas
donde aplique + invalidacion precisa + typecheck ok.
```

### DevOps Agent
```
Actua como DevOps Release Captain. Objetivo: [TAREA].
Implementa deploy inmutable, reproducible, sin downtime.
healthz/readyz, rolling update, backups.
Entrega: Dockerfile/traefik changes, scripts reproducibles.
```

### QA Agent
```
Actua como QA Engineer. Objetivo: test suite para [FLUJO].
Tests portables (sqlite en CI), sin dependencias externas.
Cubre: happy path + edge cases + errores esperados.
Entrega: feature tests + reporte de riesgos.
```

## Cuando Usar Cada Modelo

| Tarea | Modelo | Razon |
|-------|--------|-------|
| Scaffolding (CRUD, migracion) | Haiku/Sonnet | Rapido, barato, patron conocido |
| Logica de negocio compleja | Sonnet | Balance costo/calidad |
| Arquitectura, refactoring grande | Opus | Necesita entender contexto amplio |
| Review de codigo | Sonnet | Eficiente para buscar patterns |
| Debugging complejo | Opus | Necesita razonar sobre multiples sistemas |

## Coordinacion Multi-Agente

Cuando multiples agentes trabajan en el mismo codebase:
1. Cada agente trabaja en su branch. Nunca dos agentes en el mismo branch.
2. Branches se nombran: `agent/tipo-tarea` (ej: `agent/be-housekeeping-endpoint`).
3. Un humano revisa y mergea. Los agentes no mergean.
4. Si un agente necesita el trabajo de otro, espera al merge — no referencia branches remotos.

## CLAUDE.md como Contexto

Tu `CLAUDE.md` es el onboarding del agente. Debe incluir:
- Stack y versiones.
- Patrones obligatorios (API Resources, React Query, etc.).
- Lo que NUNCA hacer (Supabase, model dumps, any, etc.).
- Estructura de directorios.
- Comandos de build/test/deploy.

Si el CLAUDE.md esta desactualizado, el agente producira codigo desalineado.
