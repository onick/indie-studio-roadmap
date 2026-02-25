# Patrones de Deuda Tecnica

## Los 7 Tipos de Deuda en B2B SaaS

### 1. Deuda de Migracion
Codigo legacy coexistiendo con el nuevo. Ejemplo: Supabase auth junto a Laravel Sanctum.
**Interes:** Alto. Dos sistemas haciendo lo mismo = bugs duplicados, confusion, doble mantenimiento.

### 2. Deuda de Schema
Tablas sin columnas necesarias, relaciones rotas, indices faltantes. Ejemplo: `folio_charges` sin `hotel_id` cuando todo el sistema es multi-tenant.
**Interes:** Critico. Un query sin indice se vuelve letal cuando creces.

### 3. Deuda de UI
Componentes inconsistentes, estilos hardcodeados, modales sin scroll en movil, estados faltantes (loading/empty/error).
**Interes:** Medio. No rompe la app pero erosiona confianza del usuario.

### 4. Deuda de Tests
Flujos criticos sin tests. El checkout funciona, pero nadie lo verifico automaticamente. Cada deploy es un acto de fe.
**Interes:** Alto. Sin tests, cada fix puede romper algo mas.

### 5. Deuda de Infraestructura
Backups manuales, deploys sin healthcheck, secretos en archivos sin rotacion, logs sin rotar.
**Interes:** Catastrofico cuando falla. Invisible mientras funciona.

### 6. Deuda de Dependencias
Paquetes desactualizados, versiones con vulnerabilidades conocidas, dependencias abandonadas.
**Interes:** Lento pero compounding. Un dia una dependencia critica tiene un CVE.

### 7. Deuda de Documentacion
CLAUDE.md desactualizado, README que miente, endpoints sin documentar.
**Interes:** Bajo para humanos, alto para agentes AI que dependen de la documentacion.

## Clasificar Severidad

| Severidad | Criterio | Ejemplo |
|-----------|----------|---------|
| Critica | Bloquea usuarios o compromete datos | Multi-tenancy sin scope por hotel_id |
| Alta | Degrada rendimiento o bloquea desarrollo | N+1 query en endpoint principal |
| Media | Incomodo pero funcional | Modal sin boton cerrar en movil |
| Baja | Cosmetico o solo afecta DX | Variable mal nombrada |

## Cuando Pagar

**Inmediatamente:** Severidad critica. No espera sprint.
**Proximo sprint:** Severidad alta. Se agenda en el 20% de deuda.
**Cuando toque ese modulo:** Severidad media. Se paga cuando trabajas en esa area.
**Nunca (aceptar):** Severidad baja + modulo estable. Documentar que se acepta.

## Template para debt-register.md

```markdown
## DEBT-XXX: [Titulo corto y claro]
- Severidad: [critica|alta|media|baja]
- Modulo: [nombre-del-modulo]
- Archivo(s): [rutas relevantes]
- Registrado: YYYY-MM-DD (Sprint N)
- Deadline: Sprint N+M (o "aceptada")
- Estado: [pendiente|en_progreso|pagada|aceptada]
- Descripcion: [que es el problema]
- Impacto si no se paga: [que pasa si lo ignoras]
- Plan de pago: [como se resuelve, estimacion de esfuerzo]
```
