# Metricas y KPIs

## Pre-Launch (Mientras Construyes)

Estas son las unicas metricas que importan antes de tener clientes:

### Completion Rate
Modulos terminados / modulos totales. Esta es LA metrica. Si tienes 12 modulos planeados y 3 terminados, estas al 25%.

### Deploy Frequency
Cuantas veces deployaste a staging esta semana? Si es 0, algo esta mal. Un indie studio sano deploya a staging 3-5 veces por semana.

### Bug Density
Bugs encontrados / modulo. Si un modulo tiene 5x mas bugs que el promedio, la arquitectura de ese modulo esta mal — no son bugs aislados.

### Sprint Velocity
Tareas completadas / tareas planeadas por sprint. Trackea en metrics.md. Despues de 3 sprints tienes un baseline confiable para planificar.

### Test Coverage Trend
No el numero absoluto (que miente). La tendencia: esta subiendo o bajando? Si cada sprint agrega features sin tests, estas acumulando deuda.

## Post-Launch (Cuando Tienes Clientes)

### MRR (Monthly Recurring Revenue)
Ingreso recurrente mensual. La metrica reina de SaaS. Trackea mensualmente.

### Churn Rate
Clientes que cancelan / clientes totales por mes. Para B2B SaaS hotelero, churn > 5% mensual es una emergencia.

### Time to First Value (TTFV)
Cuanto tiempo entre que el hotel se registra y ejecuta su primer check-in exitoso? Si son mas de 30 minutos, el onboarding necesita trabajo.

### Support Tickets por Modulo
Que modulos generan mas soporte? Esos modulos necesitan UX review o documentacion.

### NPS (Net Promoter Score)
Pregunta simple: "Del 1 al 10, recomendarias este producto?" Cada 3 meses.

## Metricas de Vanidad (Ignora)

- Lineas de codigo
- Numero de commits
- Numero de endpoints
- Tareas "cerradas" en Jira/Notion
- Stars en GitHub
- Tiempo en reuniones

## Como Trackear con Markdown

No necesitas dashboards ni Mixpanel para empezar. Usa `metrics.md`:

```markdown
## Velocidad
| Sprint | Planificadas | Completadas | Velocity |
|--------|-------------|-------------|----------|
| S01    | 8           | 6           | 75%      |
| S02    | 7           | 7           | 100%     |
| S03    | 8           | 5           | 63%      |

Baseline: ~75% average velocity. Planificar 6-7 tareas por sprint.
```

Cuando tengas clientes, agrega seccion Post-Launch con MRR, churn, etc. actualizados mensualmente.
