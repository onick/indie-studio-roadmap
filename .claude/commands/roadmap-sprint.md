# /roadmap-sprint — Planificar Proximo Sprint

Eres el **Indie Studio Roadmap Orchestrator**. Planifica el proximo sprint basandote en datos reales.

## Instrucciones

### Paso 1: Analizar Sprint Actual

Lee `.indie-studio/sprint-current.md` y determina:

1. Cuantas tareas se completaron vs. planificadas.
2. Que tareas quedaron incompletas y por que.
3. Hay deuda que se arrastro del sprint anterior?

### Paso 2: Calcular Velocity

Lee `.indie-studio/metrics.md`:

1. Promedio de velocity de los ultimos 3 sprints.
2. Si no hay historial, usa 70% como baseline conservador.
3. Capacidad = velocity promedio * tareas que caben en el sprint.

### Paso 3: Seleccionar Tareas

Basandote en `.indie-studio/roadmap.md` y `.indie-studio/modules.md`:

1. Identifica el modulo prioritario segun la fase actual.
2. Descompone en vertical slices (BE + FE + test por slice).
3. Cada tarea debe caber en 2-4 horas de trabajo.
4. Ordena por dependencias (no puedes hacer slice 2 sin slice 1).

### Paso 4: Asignar 20% a Deuda

Lee `.indie-studio/debt-register.md`:

1. Si hay deuda critica o con deadline este sprint: incluirla obligatoriamente.
2. Si no hay deuda urgente: tomar la deuda alta mas antigua.
3. 20% de la capacidad del sprint va a deuda. No negociable.

### Paso 5: Validar

Antes de presentar, verifica:

- [ ] Cada tarea cabe en una sesion de trabajo (2-4 horas)?
- [ ] Las tareas estan en orden de dependencias?
- [ ] Hay 20% de capacidad asignada a deuda?
- [ ] El total es realista segun velocity historica?
- [ ] Las tareas tienen scope preciso (no "construir modulo X")?

### Paso 6: Presentar Sprint

```markdown
# Sprint [N+1]: [Nombre]
Inicio: [fecha] | Fin: [+2 semanas]
Fase: [N]
Capacidad estimada: [N tareas] (basado en velocity [X]%)

## Objetivo
[1 frase clara]

## Tareas
- [ ] TASK-XXX: [Descripcion precisa] (modulo: X, tipo: feat, estimado: Nh)
- [ ] TASK-XXX: [Descripcion precisa] (modulo: X, tipo: feat, estimado: Nh)
...

## Deuda (20%)
- [ ] DEBT-XXX: [Descripcion] (severidad: alta)

## Riesgos
- [riesgo identificado y mitigacion]
```

### Paso 7: Confirmar y Guardar

Pregunta al usuario si aprueba el sprint. Si aprueba:

1. Mueve sprint-current.md actual a sprint-log.md (como completado o parcial).
2. Escribe el nuevo sprint-current.md.
3. Actualiza metrics.md con velocity del sprint que termina.

### Reglas

- Nunca planifiques mas de lo que la velocity historica permite.
- Si el usuario quiere agregar algo extra, advierte el riesgo de sobrecarga.
- Tareas para agentes AI deben tener scope ultra-especifico (endpoint + test + deploy).
- No incluyas tareas vagas como "mejorar UX" o "refactorizar codigo".
