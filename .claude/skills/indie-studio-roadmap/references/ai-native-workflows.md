# Workflows AI-Native

## El Bucle de Orquestacion (v2.0)

El patron fundamental del Orchestrator v2.0. Reemplaza el "sandwich" simple con un flujo de 4 fases con aprobacion humana obligatoria en cada transicion.

```
┌─────────────────────────────────────────┐
│  🧠 ARQUITECTO                          │
│  Analiza peticion → Plan 3-5 pasos     │
│  Evalua vs roadmap y sprint            │
│  ⏸️ "¿Apruebas el plan?"               │
├─────────────────────────────────────────┤
│  👨‍💻 AGENTE BACKEND                      │
│  Endpoints, controllers, validaciones  │
│  Tests backend incluidos               │
│  ⏸️ "¿Apruebas la API?"                │
├─────────────────────────────────────────┤
│  ⚛️ AGENTE FRONTEND                      │
│  UI → Hooks → Services                 │
│  Consume la API del paso anterior      │
│  ⏸️ "¿Apruebas la UI?"                 │
├─────────────────────────────────────────┤
│  🧪 AGENTE QA & RELEASE                 │
│  Tests integracion, CI/CD, changelog   │
│  Actualiza estado persistente          │
│  ✅ Listo para commit                   │
└─────────────────────────────────────────┘
```

### Por que funciona

El patron sandwich original (Humano → AI → Humano) tenia un problema: el agente ejecutaba todo de golpe y el humano revisaba un bloque masivo. Si algo estaba mal en la API, el frontend ya estaba construido sobre ella — todo se descartaba.

El bucle de 4 fases corta eso. Si el humano rechaza la API en el paso 2, no se pierde trabajo de frontend. Si rechaza el plan en el paso 1, no se pierde ni una linea de codigo.

### Variantes del bucle

| Tipo de tarea | Fases | Pausas |
|--------------|-------|--------|
| Feature completa | 🧠 → 👨‍💻 → ⚛️ → 🧪 | 3 pausas |
| Solo backend | 🧠 → 👨‍💻 → 🧪 | 2 pausas |
| Solo frontend | 🧠 → ⚛️ → 🧪 | 2 pausas |
| Bug fix | 🧠 → [donde aplique] → 🧪 | 1-2 pausas |
| Hotfix critico | 🧠+[impl]+🧪 comprimido | 1 pausa (minimo) |

### Que medir del bucle

- **Approval Rate:** % de pasos aprobados al primer intento. Target: > 80%.
- **Rechazos por fase:** Si el backend siempre se aprueba pero el frontend se rechaza, el scope de UI es impreciso.
- **Rechazos por tipo:** Si features nuevas se rechazan mas que fixes, el analisis del Arquitecto necesita mas contexto.

Registrar rechazos en `sprint-log.md` como aprendizaje:
```markdown
### Rechazos del Piloto
- TASK-005 paso 3 (Frontend): Rechazado porque no manejaba estado empty. Leccion: siempre incluir los 4 estados.
```

---

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

### Scope por fase del bucle

En v2.0, cada fase del bucle tiene su propio scope:

**Arquitecto:** "Para TASK-XXX necesito: 1 endpoint GET, 1 componente tabla, 1 hook. Depende de modulo Auth. Riesgo: N+1 si no eager load."

**Backend:** "Crea GET /api/rooms con paginacion. Eager load room_type y reservations activas. Valida hotel_id del user. Test: happy path + sin auth + hotel ajeno."

**Frontend:** "Componente RoomTable que consume GET /api/rooms. Hook useRooms() con React Query. Estados: loading (skeleton), error (retry button), empty (mensaje), success (tabla). Click en row abre detalle."

**QA:** "Verifica que GET /api/rooms retorna 200 con datos, 401 sin token, 403 con hotel ajeno. Verifica que RoomTable muestra los 4 estados. Actualiza modules.md: rooms backend 100%, frontend 80%."

---

## Prompts para Agentes por Rol

### Backend Agent
```
Actua como Senior Backend Engineer. Objetivo: [TAREA].
Reglas: validaciones estrictas en toda entrada, evitar N+1,
transacciones en operaciones multi-tabla, idempotencia
en webhooks, scope siempre por tenant_id.
Entrega: controller + validations + tests + notas de seguridad.
```

### Frontend Agent
```
Actua como Senior Frontend Engineer. Arquitectura:
UI → Hooks (data fetching library) → Services → API layer.
TS estricto (sin any). Estados obligatorios: loading/empty/error/success.
Entrega: componente + hook + mutaciones optimistas
donde aplique + invalidacion precisa + typecheck ok.
```

### DevOps Agent
```
Actua como DevOps Release Captain. Objetivo: [TAREA].
Implementa deploy inmutable, reproducible, sin downtime.
healthz/readyz, rolling update, backups.
Entrega: config changes, scripts reproducibles, rollback plan.
```

### QA Agent
```
Actua como QA Engineer. Objetivo: test suite para [FLUJO].
Tests portables (sin dependencias externas en CI).
Cubre: happy path + edge cases + errores esperados.
Entrega: tests + reporte de riesgos + state updates.
```

---

## Cuando Usar Cada Modelo

| Tarea | Modelo | Razon |
|-------|--------|-------|
| Scaffolding (CRUD, migracion) | Haiku/Sonnet | Rapido, barato, patron conocido |
| Logica de negocio compleja | Sonnet | Balance costo/calidad |
| Arquitectura, refactoring grande | Opus | Necesita entender contexto amplio |
| Review de codigo | Sonnet | Eficiente para buscar patterns |
| Debugging complejo | Opus | Necesita razonar sobre multiples sistemas |

---

## Coordinacion Multi-Agente

Cuando multiples agentes trabajan en el mismo codebase:
1. Cada agente trabaja en su branch. Nunca dos agentes en el mismo branch.
2. Branches se nombran: `agent/tipo-tarea` (ej: `agent/be-housekeeping-endpoint`).
3. Un humano revisa y mergea. Los agentes no mergean.
4. Si un agente necesita el trabajo de otro, espera al merge — no referencia branches remotos.

### Delegacion con `/roadmap-delegate`

Usa el comando `/roadmap-delegate TASK-XXX` para generar prompts de delegacion precisos. El comando:
1. Lee el contexto de la tarea (modulo, deuda, estado).
2. Determina en que fase del bucle esta.
3. Genera un prompt copy-pasteable para el agente correcto.
4. Incluye archivos a tocar y criterios de aceptacion.

---

## CLAUDE.md como Contexto

Tu `CLAUDE.md` es el onboarding del agente. Debe incluir:
- Stack y versiones.
- Patrones obligatorios del proyecto.
- Lo que NUNCA hacer.
- Estructura de directorios.
- Comandos de build/test/deploy.

Si el CLAUDE.md esta desactualizado, el agente producira codigo desalineado.
