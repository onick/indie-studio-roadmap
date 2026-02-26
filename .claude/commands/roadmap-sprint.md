# /roadmap-sprint — Planificar Proximo Sprint

Eres el **Indie Studio Orchestrator v2.0**. Planifica el proximo sprint asignando tareas con fases del Bucle de Orquestacion.

## Instrucciones

### Paso 1: Analizar Sprint Actual

Lee `.indie-studio/sprint-current.md` y determina:

1. Cuantas tareas se completaron vs. planificadas.
2. Que tareas quedaron incompletas y por que.
3. Hay deuda que se arrastro del sprint anterior?
4. **Nuevo:** Revisa rechazos del Piloto en sprint-log.md — patrones a corregir?

### Paso 2: Calcular Velocity y Approval Rate

Lee `.indie-studio/metrics.md`:

1. Promedio de velocity de los ultimos 3 sprints.
2. Si no hay historial, usa 70% como baseline conservador.
3. Capacidad = velocity promedio * tareas que caben en el sprint.
4. **Nuevo:** Revisa Approval Rate — si < 70%, reduce scope por tarea (tareas mas pequenas = menos rechazos).

### Paso 3: Seleccionar Tareas

Basandote en `.indie-studio/roadmap.md` y `.indie-studio/modules.md`:

1. Identifica el modulo prioritario segun la fase actual.
2. Descompone en vertical slices (BE + FE + test por slice).
3. Cada tarea debe caber en 2-4 horas de trabajo.
4. Ordena por dependencias (no puedes hacer slice 2 sin slice 1).

### Paso 4: Asignar Fases del Bucle

Para CADA tarea, pre-asigna que fases del Bucle de Orquestacion necesita:

| Tipo de tarea | Fases del Bucle |
|--------------|-----------------|
| Feature completa (BE + FE) | 🧠 → 👨‍💻 → ⚛️ → 🧪 (4 fases) |
| Solo backend (API, migration) | 🧠 → 👨‍💻 → 🧪 (3 fases) |
| Solo frontend (UI fix, UX) | 🧠 → ⚛️ → 🧪 (3 fases) |
| Bug fix | 🧠 → [donde aplique] → 🧪 (2-3 fases) |
| Deuda tecnica | 🧠 → [donde aplique] → 🧪 (2-3 fases) |

Esto permite estimar mejor: una tarea de 4 fases toma mas que una de 2.

### Paso 5: Asignar 20% a Deuda

Lee `.indie-studio/debt-register.md`:

1. Si hay deuda critica o con deadline este sprint: incluirla obligatoriamente.
2. Si no hay deuda urgente: tomar la deuda alta mas antigua.
3. 20% de la capacidad del sprint va a deuda. No negociable.

### Paso 6: Validar

Antes de presentar, verifica:

- [ ] Cada tarea cabe en una sesion de trabajo (2-4 horas)?
- [ ] Las tareas estan en orden de dependencias?
- [ ] Hay 20% de capacidad asignada a deuda?
- [ ] El total es realista segun velocity historica?
- [ ] Las tareas tienen scope preciso (no "construir modulo X")?
- [ ] Cada tarea tiene fases del bucle asignadas?

### Paso 7: Presentar Sprint

```markdown
# Sprint [N+1]: [Nombre]
Inicio: [fecha] | Fin: [+2 semanas]
Fase: [N]
Capacidad estimada: [N tareas] (basado en velocity [X]%)

## Objetivo
[1 frase clara]

## Tareas
- [ ] TASK-XXX: [Descripcion precisa]
      Modulo: X | Tipo: feat | Estimado: Nh
      Bucle: 🧠 → 👨‍💻 → ⚛️ → 🧪 (4 fases)
      Delegable: [si/no — si es si, que agente]

- [ ] TASK-XXX: [Descripcion precisa]
      Modulo: X | Tipo: fix | Estimado: Nh
      Bucle: 🧠 → 👨‍💻 → 🧪 (3 fases, solo backend)
      Delegable: [si/no]
...

## Deuda (20%)
- [ ] DEBT-XXX: [Descripcion] (severidad: alta)
      Bucle: 🧠 → [fase] → 🧪

## Riesgos
- [riesgo identificado y mitigacion]

## Metricas del Sprint Anterior
- Velocity: X/Y (Z%)
- Approval Rate: W% ([N] rechazos)
- Ajustes aplicados: [basado en rechazos]
```

### Paso 8: Confirmar y Guardar

Pregunta al usuario si aprueba el sprint. Si aprueba:

1. Mueve sprint-current.md actual a sprint-log.md (como completado o parcial).
2. Escribe el nuevo sprint-current.md con formato v2.0 (incluye campo Bucle por tarea).
3. Actualiza metrics.md con velocity y approval rate del sprint que termina.

### Reglas

- Nunca planifiques mas de lo que la velocity historica permite.
- Si el usuario quiere agregar algo extra, advierte el riesgo de sobrecarga.
- Tareas para agentes AI deben tener scope ultra-especifico (endpoint + test + deploy).
- No incluyas tareas vagas como "mejorar UX" o "refactorizar codigo".
- Si el Approval Rate del sprint anterior fue < 70%, incluye una nota explicando que ajustes haces para mejorar (tareas mas pequenas, scope mas preciso, etc.).
- Cada tarea DEBE tener el campo Bucle asignado — esto no es opcional.
