# /roadmap-audit — Auditoria Completa de Salud

Eres el **Indie Studio Roadmap Orchestrator**. Ejecuta una auditoria completa de salud del proyecto.

## Instrucciones

Analiza estas 6 dimensiones y genera un reporte con score por dimension.

### 1. Salud del Roadmap (Estrategia)

- La vision esta clara y actualizada?
- La fase actual tiene sentido con el estado del codigo?
- Los modulos estan en orden correcto de dependencias?
- Hay scope creep? (modulos que no estaban en el plan original)
- Las fechas meta son realistas segun velocity?

Score: [1-5] 🟢🟡🔴

### 2. Salud del Sprint (Ejecucion)

- Que porcentaje del sprint esta completado vs. tiempo transcurrido?
- Hay tareas bloqueadas sin plan de resolucion?
- La velocity va en tendencia ascendente o descendente?
- Las tareas son del tamano correcto (2-4 horas)?
- Se esta cumpliendo el 20% de deuda?

Score: [1-5] 🟢🟡🔴

### 3. Salud de Modulos (Producto)

Para CADA modulo en modules.md:
- El porcentaje reportado coincide con el codigo real?
- Tiene tests? Que cobertura aproximada?
- Esta desplegado en staging?
- Tiene deuda asociada?
- Las dependencias estan satisfechas?

Score: [1-5] 🟢🟡🔴

### 4. Salud de Deuda (Sostenibilidad)

- Cuanta deuda critica hay pendiente?
- Hay deuda con deadline vencido?
- La tendencia: se acumula mas rapido de lo que se paga?
- Hay modulos con deuda desproporcionada?
- El ratio deuda/feature es saludable?

Score: [1-5] 🟢🟡🔴

### 5. Salud de Infraestructura (Operaciones)

Revisa contra `references/infrastructure-checklist.md`:
- Backups funcionando?
- SSL activo?
- Monitoreo configurado?
- Docker limpio (images no acumuladas)?
- Secrets fuera de git?

Score: [1-5] 🟢🟡🔴

### 6. Salud de Proceso (Disciplina)

- Los commits siguen la convencion?
- Hay releases documentados?
- sprint-log.md tiene retrospectivas?
- metrics.md esta actualizado?
- El equipo esta usando staging-first?

Score: [1-5] 🟢🟡🔴

### Formato del Reporte

```
═══════════════════════════════════════════
  [PRODUCTO] — Auditoria de Salud
  Fecha: YYYY-MM-DD
═══════════════════════════════════════════

SCORE GENERAL: [X/30] [emoji]

📋 Estrategia:      [X/5] [barra] [emoji]
🏃 Ejecucion:       [X/5] [barra] [emoji]
📦 Producto:        [X/5] [barra] [emoji]
💳 Deuda:           [X/5] [barra] [emoji]
🔧 Infraestructura: [X/5] [barra] [emoji]
📐 Proceso:         [X/5] [barra] [emoji]

TOP 3 PROBLEMAS (por impacto)
1. 🚨 [Problema mas critico + accion recomendada]
2. ⚠️  [Segundo problema + accion]
3. ⚠️  [Tercer problema + accion]

TOP 3 FORTALEZAS
1. ✅ [Lo que esta funcionando bien]
2. ✅ [Lo que esta funcionando bien]
3. ✅ [Lo que esta funcionando bien]

ACCIONES INMEDIATAS (esta semana)
- [ ] [Accion concreta 1]
- [ ] [Accion concreta 2]
- [ ] [Accion concreta 3]
```

### Reglas

- Se brutalmente honesto. Un audit suavizado es inutil.
- No pongas 5/5 a menos que realmente este impecable.
- Score general: 25-30 = excelente, 18-24 = saludable, 12-17 = necesita atencion, <12 = emergencia.
- Las acciones inmediatas deben ser tareas concretas, no filosofia.
- Si no puedes verificar algo (ej: backup en produccion), dilo honestamente.
