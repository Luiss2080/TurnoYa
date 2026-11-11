# Roadmap de TurnoYa

> Que el negocio nunca doble-agende un turno y que cada cliente reciba su recordatorio sin que nadie tenga que acordarse.

Ruta de desarrollo del MVP, fase por fase. Cada fase termina con algo que se puede **usar y probar en un teléfono**. Trabajá una fase a la vez y no pases a la siguiente hasta cumplir su criterio de salida.

## Resumen

- **Objetivo del MVP:** Agenda de turnos con recordatorios locales y confirmación por WhatsApp, sin internet.
- **Usuario:** Dueño de una peluquería, consultorio o taller que atiende 5 a 30 turnos por día, solo o con un asistente.
- **Duración estimada:** ~20 días de trabajo efectivo (~4 semanas a ritmo tranquilo).
- **Costo:** $0 (sin servidores, sin suscripciones, todo offline en SQLite).

## Línea de tiempo

| Fase | Nombre | Objetivo | Días | Semana acumulada |
|---|---|---|---|---|
| 1 | Estructura base y navegación | Crear el proyecto Expo con base de datos, navegación, tema y CI. | 3 | 1 |
| 2 | Clientes y servicios | Clientes, servicios y horarios. | 4 | 2 |
| 3 | Agenda y turnos | Agenda y turnos sin choques. | 6 | 3 |
| 4 | Recordatorios y WhatsApp | Recordatorios y WhatsApp. | 4 | 4 |
| 5 | Reportes del día | Reportes del día. | 3 | 4 |

## Hitos

### Hito 1 — Estructura base y navegación

**Demo:** La app abre en Expo Go, navega entre secciones vacías y el CI pasa en verde.

### Hito 2 — Clientes y servicios

**Demo:** Puedo configurar horario, 3 servicios y 10 clientes y verlos tras reiniciar.

### Hito 3 — Agenda y turnos

**Demo:** No es posible crear dos turnos superpuestos. Un turno que termina a las 10:00 y otro que empieza a las 10:00 no chocan.

### Hito 4 — Recordatorios y WhatsApp

**Demo:** Los recordatorios llegan a la hora correcta y se cancelan al cancelar el turno.

### Hito 5 — Reportes del día

**Demo:** El ingreso del día coincide con la suma de turnos atendidos.

## Detalle por fase

### Fase 1 — Estructura base y navegación

- **Objetivo:** dejar el proyecto creado y funcionando con la base de datos y la navegación.
- **Historias cubiertas:** ninguna (infraestructura).
- **Depende de:** nada.
- **Criterio de salida:** la app abre, navega y el CI está en verde.

### Fase 2 — Clientes y servicios

- **Objetivo:** Clientes, servicios y horarios.
- **Historias cubiertas:** HU-01 Definir horario de atención; HU-02 Clientes y servicios
- **Depende de:** fase 1.
- **Duración:** 4 días.
- **Criterio de salida:**
  - Puedo configurar horario, 3 servicios y 10 clientes y verlos tras reiniciar.

### Fase 3 — Agenda y turnos

- **Objetivo:** Agenda y turnos sin choques.
- **Historias cubiertas:** HU-03 Agendar un turno; HU-04 Ver agenda diaria y semanal
- **Depende de:** fase 2.
- **Duración:** 6 días.
- **Criterio de salida:**
  - No es posible crear dos turnos superpuestos.
  - Un turno que termina a las 10:00 y otro que empieza a las 10:00 no chocan.

### Fase 4 — Recordatorios y WhatsApp

- **Objetivo:** Recordatorios y WhatsApp.
- **Historias cubiertas:** HU-05 Recordatorios y confirmación por WhatsApp
- **Depende de:** fase 3.
- **Duración:** 4 días.
- **Criterio de salida:**
  - Los recordatorios llegan a la hora correcta y se cancelan al cancelar el turno.

### Fase 5 — Reportes del día

- **Objetivo:** Reportes del día.
- **Historias cubiertas:** HU-06 Resumen del día
- **Depende de:** fase 4.
- **Duración:** 3 días.
- **Criterio de salida:**
  - El ingreso del día coincide con la suma de turnos atendidos.

## Estado

- [ ] **Fase 1:** Estructura base y navegación
- [ ] **Fase 2:** Clientes y servicios
- [ ] **Fase 3:** Agenda y turnos
- [ ] **Fase 4:** Recordatorios y WhatsApp
- [ ] **Fase 5:** Reportes del día

## Dependencias y supuestos

- Un teléfono Android real para probar (Expo Go) y, al final, para instalar el APK.
- Node.js 20+ y la app Expo Go.
- Todo lo demás (cuentas, nube, pagos) queda fuera del MVP.

## Después del MVP (v1.1 en adelante)

- [ ] Modo oscuro.
- [ ] Búsqueda y filtros avanzados en las listas.
- [ ] Estadísticas y gráficos simples en la pantalla de inicio.
- [ ] Publicación en Google Play (cuenta de desarrollador, pago único de USD 25).
- [ ] Respaldo y restauración de la base de datos.
- [ ] Sincronización opcional entre dispositivos (solo si hay demanda real).

## Cómo usar este documento

1. [SPEC.md](SPEC.md) define **qué** debe hacer la app (historias, reglas, pantallas, datos).
2. [PLANNING.md](PLANNING.md) define **cómo** construirla fase por fase (tareas, pruebas, issues).
3. Al cerrar una fase: marcá el estado acá, actualizá el [CHANGELOG](../CHANGELOG.md) y fusioná la rama a `main`.

## Trazabilidad: historias por fase

| Historia | Fase | Entrega |
|---|---|---|
| HU-01 Definir horario de atención | 2 | Clientes y servicios |
| HU-02 Clientes y servicios | 2 | Clientes y servicios |
| HU-03 Agendar un turno | 3 | Agenda y turnos |
| HU-04 Ver agenda diaria y semanal | 3 | Agenda y turnos |
| HU-05 Recordatorios y confirmación por WhatsApp | 4 | Recordatorios y WhatsApp |
| HU-06 Resumen del día | 5 | Reportes del día |

## Indicadores de avance

| Indicador | Cómo se mide | Meta |
|---|---|---|
| Avance de tareas | Issues cerrados / 38 | 100 % al lanzar |
| Historias aceptadas | Casos CP- marcados ✔ / 11 | 100 % |
| Reglas cubiertas | RN con prueba / 7 | 100 % |
| Ritmo | Días reales vs estimados por fase | Desvío menor al 50 % |
| Calidad | Errores abiertos al cierre de fase | 0 críticos |

## Revisión de fin de fase

Antes de pasar a la siguiente fase respondé por escrito (5 minutos):

1. ¿Se cumplieron todos los criterios de salida?
2. ¿Cuántos días tomó realmente frente a lo estimado?
3. ¿Qué se recortó o se dejó para después?
4. ¿Hay algo que cambie el SPEC? Actualizalo antes de seguir.

## Documentos de apoyo

- [DATOS.md](DATOS.md): campos, validaciones, consultas y migraciones.
- [PANTALLAS.md](PANTALLAS.md): ruta, estados y bosquejo de cada pantalla.
- [PRUEBAS.md](PRUEBAS.md): casos de prueba por regla, historia y borde.
- [BACKLOG.md](BACKLOG.md): issues listos para GitHub con milestones y etiquetas.
