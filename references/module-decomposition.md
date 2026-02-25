# Descomposicion en Modulos

## Principio: Modulos por Dominio, No por Pagina

Mal: "Modulo Dashboard", "Modulo Settings", "Modulo Profile"
Bien: "Modulo Reservaciones", "Modulo Housekeeping", "Modulo Facturacion"

Un modulo encapsula un dominio de negocio completo: datos, logica, API, y UI.

## Anatomia de un Modulo

```
[Modulo: Reservaciones]
├── Backend
│   ├── Model: Reservation, ReservationUnit
│   ├── Controller: ReservationController
│   ├── Service: ReservationService
│   ├── Resource: ReservationResource
│   └── Tests: ReservationTest
├── Frontend
│   ├── Page: ReservationsList, ReservationDetail
│   ├── Components: ReservationCard, ReservationForm
│   ├── Hooks: useReservations, useCreateReservation
│   └── Services: reservationApi.ts
└── Estado
    ├── Depende de: Auth, RoomTypes, Guests
    ├── Bloquea a: Billing, Housekeeping, Reports
    └── Deuda: [DEBT-XXX]
```

## Mapa de Dependencias Tipico (PMS)

```
Auth ──→ RoomTypes ──→ Rooms ──→ Reservaciones ──→ Check-in/out
  │                      │              │                │
  │                      └──→ Housekeeping              │
  │                                                      │
  └──→ Guests ──────────────────────────────→ Facturacion
                                                    │
                                              Reports/Analytics
```

Regla: Construye de izquierda a derecha. No puedes hacer reservaciones sin tipos de habitacion. No puedes facturar sin checkout.

## Framework de Priorizacion

Para decidir que modulo construir primero, usa esta matriz:

| Factor | Peso | Pregunta |
|--------|------|----------|
| Valor para usuario | 40% | El hotel puede operar sin esto? |
| Dependencias | 25% | Cuantos modulos dependen de este? |
| Complejidad | 20% | Cuanto esfuerzo requiere? |
| Revenue impact | 15% | Esto ayuda a cobrar/retener? |

Score = (Valor * 0.4) + (Dependencias * 0.25) + (1/Complejidad * 0.2) + (Revenue * 0.15)

## Vertical Slice (Como Construir un Modulo)

No construyas todo el backend y luego todo el frontend. Construye en slices verticales:

```
Slice 1: Listar reservaciones
  BE: GET /reservations (con paginacion, scope hotel_id)
  FE: ReservationsList page + useReservations hook
  Test: Feature test del endpoint + typecheck

Slice 2: Crear reservacion
  BE: POST /reservations (con validacion, availability check)
  FE: ReservationForm + useCreateReservation
  Test: Feature test + test de edge cases

Slice 3: Ver detalle
  BE: GET /reservations/{id} (con relaciones)
  FE: ReservationDetail page
  Test: Feature test
```

Cada slice es desplegable independientemente. Slice 1 ya es util para ver que hay.

## Estado de Modulos

Cada modulo pasa por estas etapas:

1. **no-iniciado** — Existe en el roadmap pero no hay codigo.
2. **en-progreso** — Al menos un slice esta construido.
3. **staging** — Modulo completo desplegado en staging y verificado.
4. **produccion** — Desplegado en produccion y usado por clientes.
5. **deprecado** — Reemplazado o eliminado.

En `modules.md`, cada modulo tiene su estado actual y porcentaje de completitud por capa (backend/frontend/tests).

## Anti-patrones

- **Modulo dios:** Un modulo que hace todo (ej: "Dashboard" que tiene KPIs, reservas, housekeeping, alerts). Dividelo.
- **Modulo fantasma:** Existe en el plan pero lleva 3+ sprints sin avance. Eliminalo del sprint y ponlo en backlog honestamente.
- **Modulo perfectamente inutil:** Backend 100%, frontend 100%, tests 100%... pero ningun usuario lo necesitaba. Valida antes de construir.
