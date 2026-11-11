# Plan de desarrollo — TurnoYa

Guía para construir el MVP **fase por fase**. Complementa a [ROADMAP.md](ROADMAP.md) (qué y cuándo) y [SPEC.md](SPEC.md) (qué debe hacer la app).

## Forma de trabajo

1. **Una fase por rama:** `git checkout -b fase-N-nombre`.
2. **Un issue por tarea** (usá los títulos de abajo) y un milestone por fase en GitHub.
3. **Commits chicos** con prefijo: `feat:`, `fix:`, `test:`, `docs:`, `chore:`.
4. **Pull request al cerrar la fase** con la lista de criterios de salida marcada.
5. **Primero la lógica pura y sus pruebas** (en `src/lib/`), después la pantalla que la usa.
6. **Probá en el teléfono** al terminar cada tarea de pantalla, no solo al final de la fase.

### Definición de «terminado» (vale para toda tarea y fase)

- [ ] `npm run lint`, `npx tsc --noEmit` y `npm test` sin errores.
- [ ] Funciona en modo avión.
- [ ] Estados vacíos, de carga y de error resueltos, con textos en español.
- [ ] Los datos persisten al reiniciar la app.
- [ ] La regla de negocio correspondiente tiene prueba o verificación manual anotada.
- [ ] CHANGELOG actualizado al cerrar la fase.

## Calendario sugerido

| Fase | Nombre | Días | Acumulado |
|---|---|---|---|
| 1 | Estructura base y navegación | 3 | 3 |
| 2 | Clientes y servicios | 4 | 7 |
| 3 | Agenda y turnos | 6 | 13 |
| 4 | Recordatorios y WhatsApp | 4 | 17 |
| 5 | Reportes del día | 3 | 20 |

Total: **20 días** (~4 semanas). Si una fase se pasa del doble de lo estimado, recortá alcance dentro de la fase en lugar de saltar a la siguiente.

---

## Fase 1 — Estructura base y navegación

**Estimación:** 3 días.

**Objetivo:** crear el proyecto Expo con base de datos, navegación, tema y CI. Este repositorio hoy solo tiene documentación; la fase 1 agrega el código base.

**Tareas**

- [ ] **F1-T1** Crear el proyecto en una carpeta temporal con `npx create-expo-app@latest` (plantilla con TypeScript y Expo Router) y copiar su contenido a este repositorio sin pisar README, LICENSE ni los archivos de comunidad.
- [ ] **F1-T2** Alinear versiones y herramientas con los repositorios hermanos (por ejemplo PasanakuYa): Expo SDK 58, `expo-sqlite`, `jest-expo`, ESLint, TypeScript estricto, `.editorconfig` y `.prettierrc`.
- [ ] **F1-T3** Crear la estructura `app/`, `src/db/`, `src/components/`, `src/lib/` y `src/theme.ts`.
- [ ] **F1-T4** Implementar `src/db/index.ts` (apertura de la base) y `src/db/schema.ts` con la migración 1 tomada de la sección 6 del [SPEC](SPEC.md).
- [ ] **F1-T5** Definir la navegación principal en `app/_layout.tsx` con una ruta por sección.
- [ ] **F1-T6** Agregar el componente `Pantalla`, el tema y la pantalla de inicio con accesos a cada sección.
- [ ] **F1-T7** Configurar scripts `lint`, `test`, `build:apk` y el workflow de GitHub Actions (`.github/workflows/ci.yml`).
- [ ] **F1-T8** Escribir una primera prueba de `src/lib` para validar que Jest funciona.

**Criterio de salida:** la app abre, navega entre secciones vacías y no da errores; lint, tipos y tests en verde.

---

## Fase 2 — Clientes y servicios

**Estimación:** 4 días. **Rama sugerida:** `fase-2-clientes-y-servicios`.

**Objetivo:** Clientes, servicios y horarios.

**Historias que se completan:** HU-01 (Definir horario de atención), HU-02 (Clientes y servicios).

**Tareas**

- [ ] **F2-T1** CRUD de clientes y servicios con baja lógica.
- [ ] **F2-T2** Pantalla de horarios por día de la semana con valores por defecto.
- [ ] **F2-T3** Validación de duración (múltiplos de 5 minutos) y precio.
- [ ] **F2-T4** Normalización de teléfono (+591).
- [ ] **F2-T5** Pruebas de validación.
- [ ] **F2-T6** Validaciones, estados vacíos y mensajes de error en español para todo lo nuevo.
- [ ] **F2-T7** Pruebas automáticas de la lógica de la fase y verificación manual de los criterios de abajo.
- [ ] **F2-T8** Probar en un teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.

**Pruebas de aceptación manuales** (de [SPEC.md](SPEC.md)):

- [ ] HU-01 — un horario lunes a sábado de 9 a 18 → abro la agenda del domingo → veo el día como cerrado.
- [ ] HU-01 — un horario con «hasta» anterior a «desde» → intento guardar → la app lo rechaza.
- [ ] HU-02 — un servicio de 45 minutos y 60 Bs → lo guardo → queda disponible al agendar.
- [ ] HU-02 — un cliente sin teléfono → lo guardo → se guarda pero no podré confirmar por WhatsApp.

**Criterio de salida:**

- [ ] Puedo configurar horario, 3 servicios y 10 clientes y verlos tras reiniciar.
- [ ] Definición de «terminado» cumplida.

---

## Fase 3 — Agenda y turnos

**Estimación:** 6 días. **Rama sugerida:** `fase-3-agenda-y-turnos`.

**Objetivo:** Agenda y turnos sin choques.

**Historias que se completan:** HU-03 (Agendar un turno), HU-04 (Ver agenda diaria y semanal).

**Tareas**

- [ ] **F3-T1** Función pura de detección de choques y huecos libres con pruebas exhaustivas (bordes exactos, turnos consecutivos).
- [ ] **F3-T2** Vista de agenda diaria con huecos y vista semanal.
- [ ] **F3-T3** Formulario de turno que solo ofrece horas libres.
- [ ] **F3-T4** Mover, cancelar y marcar estados del turno.
- [ ] **F3-T5** Copia del precio del servicio en el turno.
- [ ] **F3-T6** Validaciones, estados vacíos y mensajes de error en español para todo lo nuevo.
- [ ] **F3-T7** Pruebas automáticas de la lógica de la fase y verificación manual de los criterios de abajo.
- [ ] **F3-T8** Probar en un teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.

**Pruebas de aceptación manuales** (de [SPEC.md](SPEC.md)):

- [ ] HU-03 — un horario libre → agendo un servicio de 45 minutos → el turno ocupa el bloque de 45 minutos en la agenda.
- [ ] HU-03 — un horario ocupado → intento agendar encima → la app lo bloquea y propone el próximo horario libre.
- [ ] HU-04 — un día con 6 turnos → abro la agenda → veo los turnos y los huecos libres en orden.
- [ ] HU-04 — la vista semanal → la abro → veo la carga por día.

**Criterio de salida:**

- [ ] No es posible crear dos turnos superpuestos.
- [ ] Un turno que termina a las 10:00 y otro que empieza a las 10:00 no chocan.
- [ ] Definición de «terminado» cumplida.

---

## Fase 4 — Recordatorios y WhatsApp

**Estimación:** 4 días. **Rama sugerida:** `fase-4-recordatorios-y-whatsapp`.

**Objetivo:** Recordatorios y WhatsApp.

**Historias que se completan:** HU-05 (Recordatorios y confirmación por WhatsApp).

**Tareas**

- [ ] **F4-T1** Notificaciones locales con expo-notifications y minutos de aviso configurables.
- [ ] **F4-T2** Cancelar o reprogramar la notificación al mover o cancelar un turno.
- [ ] **F4-T3** Mensaje de confirmación por WhatsApp con plantilla editable.
- [ ] **F4-T4** Pantalla Ajustes de avisos y plantillas.
- [ ] **F4-T5** Validaciones, estados vacíos y mensajes de error en español para todo lo nuevo.
- [ ] **F4-T6** Pruebas automáticas de la lógica de la fase y verificación manual de los criterios de abajo.
- [ ] **F4-T7** Probar en un teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.

**Pruebas de aceptación manuales** (de [SPEC.md](SPEC.md)):

- [ ] HU-05 — un turno mañana a las 10:00 → toco «Confirmar por WhatsApp» → se abre WhatsApp con el mensaje prellenado con fecha, hora y servicio.
- [ ] HU-05 — un turno en 60 minutos → llega la hora del aviso → recibo una notificación local.

**Criterio de salida:**

- [ ] Los recordatorios llegan a la hora correcta y se cancelan al cancelar el turno.
- [ ] Definición de «terminado» cumplida.

---

## Fase 5 — Reportes del día

**Estimación:** 3 días. **Rama sugerida:** `fase-5-reportes-del-dia`.

**Objetivo:** Reportes del día.

**Historias que se completan:** HU-06 (Resumen del día).

**Tareas**

- [ ] **F5-T1** Consultas de turnos por estado e ingresos del día.
- [ ] **F5-T2** Pantalla Resumen del día con selector de fecha.
- [ ] **F5-T3** Resumen semanal simple.
- [ ] **F5-T4** Exportar el resumen a CSV.
- [ ] **F5-T5** Validaciones, estados vacíos y mensajes de error en español para todo lo nuevo.
- [ ] **F5-T6** Pruebas automáticas de la lógica de la fase y verificación manual de los criterios de abajo.
- [ ] **F5-T7** Probar en un teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.

**Pruebas de aceptación manuales** (de [SPEC.md](SPEC.md)):

- [ ] HU-06 — un día con 8 atendidos y 1 que no vino → abro Resumen → veo cantidades y el ingreso total de los atendidos.

**Criterio de salida:**

- [ ] El ingreso del día coincide con la suma de turnos atendidos.
- [ ] Definición de «terminado» cumplida.

---

## Lanzamiento del MVP (v1.0.0)

- [ ] Revisar todas las historias de [SPEC.md](SPEC.md) y marcar sus criterios.
- [ ] Capturas de pantalla para el README.
- [ ] Revisar textos, íconos y nombre de la app en `app.json`.
- [ ] `npm run build:apk` y probar el APK en un teléfono limpio, sin Expo Go y en modo avión.
- [ ] Publicar el release v1.0.0 con el APK adjunto.
- [ ] Pasar las ideas pendientes a issues para la versión 1.1.

## Plantilla de issue

Copiá esto al crear cada issue:

```
Título: [F2-T1] <descripción de la tarea>
Fase: N — <nombre>
Historia: HU-XX
Criterios:
- [ ] ...
Notas técnicas: ...
```

---

## Cronograma día por día

Guía para no perder el hilo: cada día tiene tareas concretas. Si un día se atrasa, mové la tarea al siguiente, no la saltees.

### Fase 1 — Estructura base y navegación

- **Día 1** (día 1 de la fase): `F1-T1` Crear el proyecto Expo con TypeScript y Expo Router; `F1-T2` Alinear versiones y herramientas con los repos hermanos; `F1-T3` Crear la estructura de carpetas.
- **Día 2** (día 2 de la fase): `F1-T4` Implementar apertura de base y migración 1; `F1-T5` Definir la navegación principal; `F1-T6` Agregar componente Pantalla, tema e inicio.
- **Día 3** (día 3 de la fase): `F1-T7` Configurar scripts y CI; `F1-T8` Primera prueba de src/lib.

### Fase 2 — Clientes y servicios

- **Día 4** (día 1 de la fase): `F2-T1` CRUD de clientes y servicios con baja lógica.; `F2-T2` Pantalla de horarios por día de la semana con valores por defecto..
- **Día 5** (día 2 de la fase): `F2-T3` Validación de duración (múltiplos de 5 minutos) y precio.; `F2-T4` Normalización de teléfono (+591)..
- **Día 6** (día 3 de la fase): `F2-T5` Pruebas de validación.; `F2-T6` Validaciones, estados vacíos y mensajes en español.
- **Día 7** (día 4 de la fase): `F2-T7` Pruebas automáticas y verificación manual de criterios; `F2-T8` Prueba en teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.

### Fase 3 — Agenda y turnos

- **Día 8** (día 1 de la fase): `F3-T1` Función pura de detección de choques y huecos libres con pruebas exhaustivas (bordes exactos, turnos consecutivos).; `F3-T2` Vista de agenda diaria con huecos y vista semanal..
- **Día 9** (día 2 de la fase): `F3-T3` Formulario de turno que solo ofrece horas libres.; `F3-T4` Mover, cancelar y marcar estados del turno..
- **Día 10** (día 3 de la fase): `F3-T5` Copia del precio del servicio en el turno.; `F3-T6` Validaciones, estados vacíos y mensajes en español.
- **Día 11** (día 4 de la fase): `F3-T7` Pruebas automáticas y verificación manual de criterios; `F3-T8` Prueba en teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.
- **Día 12** (día 5 de la fase): colchón: terminar pendientes, probar en el teléfono y ajustar detalles.
- **Día 13** (día 6 de la fase): colchón: terminar pendientes, probar en el teléfono y ajustar detalles.

### Fase 4 — Recordatorios y WhatsApp

- **Día 14** (día 1 de la fase): `F4-T1` Notificaciones locales con expo-notifications y minutos de aviso configurables.; `F4-T2` Cancelar o reprogramar la notificación al mover o cancelar un turno..
- **Día 15** (día 2 de la fase): `F4-T3` Mensaje de confirmación por WhatsApp con plantilla editable.; `F4-T4` Pantalla Ajustes de avisos y plantillas..
- **Día 16** (día 3 de la fase): `F4-T5` Validaciones, estados vacíos y mensajes en español; `F4-T6` Pruebas automáticas y verificación manual de criterios.
- **Día 17** (día 4 de la fase): `F4-T7` Prueba en teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.

### Fase 5 — Reportes del día

- **Día 18** (día 1 de la fase): `F5-T1` Consultas de turnos por estado e ingresos del día.; `F5-T2` Pantalla Resumen del día con selector de fecha.; `F5-T3` Resumen semanal simple..
- **Día 19** (día 2 de la fase): `F5-T4` Exportar el resumen a CSV.; `F5-T5` Validaciones, estados vacíos y mensajes en español; `F5-T6` Pruebas automáticas y verificación manual de criterios.
- **Día 20** (día 3 de la fase): `F5-T7` Prueba en teléfono real en modo avión; actualizar ROADMAP y CHANGELOG.

## Rutina diaria

1. Empezá releyendo la tarea del día en [BACKLOG.md](BACKLOG.md) y su regla o historia en [SPEC.md](SPEC.md).
2. Escribí primero la prueba de la lógica, después el código.
3. Probá en el teléfono antes de hacer commit.
4. Cerrá el día con un commit, el issue marcado y una línea en el CHANGELOG si corresponde.

## Qué hacer si te trabás

- Más de 2 horas sin avanzar: simplificá la tarea al caso mínimo y anotá lo que quedó fuera como issue `mejora`.
- Duda de alcance: la respuesta está en la sección 2 del [SPEC](SPEC.md); si no está, no entra al MVP.
- Duda de datos o validaciones: [DATOS.md](DATOS.md).
- Duda de pantallas: [PANTALLAS.md](PANTALLAS.md).
- Duda de cómo probar: [PRUEBAS.md](PRUEBAS.md).
