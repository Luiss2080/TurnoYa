# Especificación del MVP — TurnoYa

> Documento vivo. Si una decisión cambia, se actualiza acá primero y después en el código.

## 1. Resumen

**Propuesta de valor:** Que el negocio nunca doble-agende un turno y que cada cliente reciba su recordatorio sin que nadie tenga que acordarse.

**Problema:** Los turnos se anotan en cuaderno o en el chat; hay choques de horario, clientes que no vienen por olvido y tiempo perdido confirmando uno por uno.

**Cómo se resuelve hoy:** Cuaderno de turnos y mensajes de WhatsApp.

**Usuario objetivo:** Dueño de una peluquería, consultorio o taller que atiende 5 a 30 turnos por día, solo o con un asistente.

**Métricas de éxito del MVP:**

- Agendar un turno en menos de 20 segundos.
- Cero choques de horario.
- Confirmar un turno por WhatsApp con un toque.

## 2. Alcance del MVP

### Incluido

- Agenda diaria y semanal con horarios libres.
- Clientes y servicios con duración y precio.
- Recordatorios locales antes de cada turno.
- Confirmación por WhatsApp pre-llenada.
- Evita choques de horario automáticamente.
- Resumen de turnos e ingresos del día.

### Fuera del MVP

- Cuentas de usuario, inicio de sesión y multiusuario.
- Servidor propio, nube o sincronización entre dispositivos.
- Pagos en línea o pasarelas de cobro.
- Publicación en App Store (iOS).
- Cualquier servicio de pago o con costo recurrente.

## 3. Reglas de negocio

Reglas que el código **debe** cumplir. Cada una se cubre con pruebas automáticas cuando es lógica pura.

- **RN-01** El fin de un turno = inicio + duración del servicio (queda guardado en `fin`).
- **RN-02** Dos turnos no pueden superponerse; el choque se valida al guardar y al mover.
- **RN-03** Solo se pueden agendar turnos dentro del horario de atención del día.
- **RN-04** Estados del turno: pendiente, confirmado, atendido, cancelado y no vino.
- **RN-05** El precio del turno se copia del servicio al agendar y no cambia si el servicio cambia después.
- **RN-06** Los ingresos del día cuentan los turnos atendidos.
- **RN-07** El recordatorio local se programa antes del inicio según los minutos configurados y se cancela si el turno se cancela.

## 4. Historias de usuario y criterios de aceptación

Formato: **Dado** un contexto, **Cuando** el usuario hace algo, **Entonces** pasa algo verificable.

### HU-01 — Definir horario de atención

**Como** dueño **quiero** configurar mis días y horas de atención **para** que la agenda solo ofrezca horas válidas.

**Fase:** 2 — Clientes y servicios

**Criterios de aceptación:**

1. **Dado:** un horario lunes a sábado de 9 a 18. **Cuando:** abro la agenda del domingo. **Entonces:** veo el día como cerrado.
2. **Dado:** un horario con «hasta» anterior a «desde». **Cuando:** intento guardar. **Entonces:** la app lo rechaza.

Criterios comunes a toda historia:

- [ ] Funciona sin conexión a internet.
- [ ] Los datos persisten al cerrar y reabrir la app.
- [ ] Los errores de validación se muestran en español y junto al campo.
- [ ] Hay un estado vacío claro cuando todavía no hay datos.

### HU-02 — Clientes y servicios

**Como** dueño **quiero** cargar clientes y servicios con duración y precio **para** agendar rápido.

**Fase:** 2 — Clientes y servicios

**Criterios de aceptación:**

1. **Dado:** un servicio de 45 minutos y 60 Bs. **Cuando:** lo guardo. **Entonces:** queda disponible al agendar.
2. **Dado:** un cliente sin teléfono. **Cuando:** lo guardo. **Entonces:** se guarda pero no podré confirmar por WhatsApp.

Criterios comunes a toda historia:

- [ ] Funciona sin conexión a internet.
- [ ] Los datos persisten al cerrar y reabrir la app.
- [ ] Los errores de validación se muestran en español y junto al campo.
- [ ] Hay un estado vacío claro cuando todavía no hay datos.

### HU-03 — Agendar un turno

**Como** dueño **quiero** elegir cliente, servicio y hora libre **para** cargar el turno en pocos toques.

**Fase:** 3 — Agenda y turnos

**Criterios de aceptación:**

1. **Dado:** un horario libre. **Cuando:** agendo un servicio de 45 minutos. **Entonces:** el turno ocupa el bloque de 45 minutos en la agenda.
2. **Dado:** un horario ocupado. **Cuando:** intento agendar encima. **Entonces:** la app lo bloquea y propone el próximo horario libre.

Criterios comunes a toda historia:

- [ ] Funciona sin conexión a internet.
- [ ] Los datos persisten al cerrar y reabrir la app.
- [ ] Los errores de validación se muestran en español y junto al campo.
- [ ] Hay un estado vacío claro cuando todavía no hay datos.

### HU-04 — Ver agenda diaria y semanal

**Como** dueño **quiero** ver mis turnos por día y semana con huecos libres **para** organizarme.

**Fase:** 3 — Agenda y turnos

**Criterios de aceptación:**

1. **Dado:** un día con 6 turnos. **Cuando:** abro la agenda. **Entonces:** veo los turnos y los huecos libres en orden.
2. **Dado:** la vista semanal. **Cuando:** la abro. **Entonces:** veo la carga por día.

Criterios comunes a toda historia:

- [ ] Funciona sin conexión a internet.
- [ ] Los datos persisten al cerrar y reabrir la app.
- [ ] Los errores de validación se muestran en español y junto al campo.
- [ ] Hay un estado vacío claro cuando todavía no hay datos.

### HU-05 — Recordatorios y confirmación por WhatsApp

**Como** dueño **quiero** recibir recordatorios y confirmar por WhatsApp **para** reducir faltas.

**Fase:** 4 — Recordatorios y WhatsApp

**Criterios de aceptación:**

1. **Dado:** un turno mañana a las 10:00. **Cuando:** toco «Confirmar por WhatsApp». **Entonces:** se abre WhatsApp con el mensaje prellenado con fecha, hora y servicio.
2. **Dado:** un turno en 60 minutos. **Cuando:** llega la hora del aviso. **Entonces:** recibo una notificación local.

Criterios comunes a toda historia:

- [ ] Funciona sin conexión a internet.
- [ ] Los datos persisten al cerrar y reabrir la app.
- [ ] Los errores de validación se muestran en español y junto al campo.
- [ ] Hay un estado vacío claro cuando todavía no hay datos.

### HU-06 — Resumen del día

**Como** dueño **quiero** ver turnos atendidos, cancelados e ingresos **para** cerrar el día.

**Fase:** 5 — Reportes del día

**Criterios de aceptación:**

1. **Dado:** un día con 8 atendidos y 1 que no vino. **Cuando:** abro Resumen. **Entonces:** veo cantidades y el ingreso total de los atendidos.

Criterios comunes a toda historia:

- [ ] Funciona sin conexión a internet.
- [ ] Los datos persisten al cerrar y reabrir la app.
- [ ] Los errores de validación se muestran en español y junto al campo.
- [ ] Hay un estado vacío claro cuando todavía no hay datos.

## 5. Pantallas y navegación

| Pantalla | Qué muestra | Acciones principales |
|---|---|---|
| Inicio | Turnos de hoy y próximo turno | Nuevo turno |
| Agenda | Vista día y semana con huecos libres | Tocar hueco para agendar, mover turno |
| Formulario de turno | Cliente, servicio, fecha, hora (solo libres) | Guardar, cancelar turno |
| Clientes | Lista con búsqueda | Crear, editar |
| Servicios | Lista con duración y precio | Crear, editar, desactivar |
| Horarios | Horario por día de la semana | Guardar |
| Resumen del día | Turnos por estado e ingresos | Cambiar fecha, exportar |

**Navegación:** la pantalla de inicio es el punto de partida; desde ahí se llega a cada sección con un toque y se vuelve con el botón atrás. Las acciones frecuentes (nuevo registro) están siempre a la vista. La navegación se implementa con Expo Router (rutas por archivo en `app/`).

## 6. Modelo de datos

Base SQLite local versionada con `PRAGMA user_version`. Las fechas se guardan como texto ISO 8601 y los montos como números reales (en bolivianos).

### `clientes`

Clientes con su teléfono.

| Campo | Tipo | Reglas |
|---|---|---|
| `id` | INTEGER | Clave primaria autoincremental |
| `nombre` | TEXT | Obligatorio |
| `telefono` | TEXT | Opcional |

### `servicios`

Servicios con duración y precio.

| Campo | Tipo | Reglas |
|---|---|---|
| `id` | INTEGER | Clave primaria autoincremental |
| `nombre` | TEXT | Obligatorio |
| `minutos` | INTEGER | Obligatorio |
| `precio` | REAL | Obligatorio; Por defecto 0 |
| `activo` | INTEGER | Obligatorio; Por defecto 1 |

### `turnos`

Cada turno con inicio, fin, estado y precio acordado (se agregan fin y precio respecto al README).

| Campo | Tipo | Reglas |
|---|---|---|
| `id` | INTEGER | Clave primaria autoincremental |
| `cliente_id` | INTEGER | Obligatorio; Referencia a clientes |
| `servicio_id` | INTEGER | Obligatorio; Referencia a servicios |
| `inicio` | TEXT | Obligatorio |
| `fin` | TEXT | Obligatorio |
| `estado` | TEXT | Obligatorio; Por defecto “pendiente” |
| `precio` | REAL | Obligatorio; Por defecto 0 |

### `horarios`

Horario de atención por día de la semana.

| Campo | Tipo | Reglas |
|---|---|---|
| `id` | INTEGER | Clave primaria autoincremental |
| `dia_semana` | INTEGER | Obligatorio |
| `desde` | TEXT | Obligatorio |
| `hasta` | TEXT | Obligatorio |

Detalle de la política de migraciones: nunca editar una migración publicada; agregar una nueva al final de la lista en `src/db/schema.ts`.

## 7. Casos borde a contemplar

- Turno que cruza la hora de cierre: no se permite.
- Cambio de la duración de un servicio: no modifica turnos ya agendados.
- Cliente repetido por teléfono: avisar y ofrecer usar el existente.
- Cambio de horario de atención con turnos ya agendados fuera del nuevo horario: se muestran como advertencia.
- Cambio de hora del teléfono por horario de verano o zona: los turnos se guardan en hora local ISO sin zona.

## 8. Requisitos no funcionales

- **Offline total:** ninguna función del MVP requiere internet.
- **Rendimiento:** la app abre en menos de 2 segundos; las listas responden de inmediato con cientos de registros (consultas con índices y listas virtualizadas).
- **Privacidad:** los datos nunca salen del teléfono, salvo lo que el usuario comparte a propósito.
- **Idioma y formato:** español de Bolivia, montos en bolivianos (Bs) con dos decimales, fechas día/mes/año.
- **Usabilidad:** botones grandes, formularios cortos y flujos de pocos toques; pensado para usarse de pie y con una mano.
- **Accesibilidad básica:** contraste suficiente, etiquetas de accesibilidad en botones y tamaño de texto que respeta el ajuste del sistema.
- **Calidad:** `npm run lint`, `npx tsc --noEmit` y `npm test` pasan en cada commit (CI en GitHub Actions).
- **Compatibilidad:** Android 8 o superior (probado con Expo Go y con el APK).

### Capacidades de Expo que usa

| Paquete | Para qué | Permiso |
|---|---|---|
| `expo-notifications` | Recordatorios locales (programados en el teléfono, sin servidor) | Notificaciones |

## 9. Riesgos y mitigaciones

| Riesgo | Mitigación |
|---|---|
| Notificaciones bloqueadas por el sistema | La agenda del día siempre es visible; guiar al usuario a los permisos. |
| Choques por concurrencia (dos personas con el mismo celular no aplica) | Validar dentro de una transacción al guardar. |
| Cambios de esquema que rompan datos | Migraciones con `PRAGMA user_version`; nunca editar una migración publicada |
| El alcance crece | Todo lo que no esté en la sección 2 va a «Después del MVP» en el [ROADMAP](ROADMAP.md) |

## 10. Criterios para lanzar el MVP (v1.0.0)

- [ ] Todas las historias de la sección 4 cumplen sus criterios de aceptación.
- [ ] Todas las reglas de negocio de la sección 3 tienen pruebas o verificación manual.
- [ ] Probado en un teléfono Android real, en modo avión.
- [ ] APK generado con `npm run build:apk` e instalado en un teléfono limpio.
- [ ] README con capturas de pantalla y CHANGELOG actualizado.
- [ ] Release v1.0.0 publicado con el APK adjunto.

## 11. Glosario

| Término | Significado en TurnoYa |
|---|---|
| `clientes` | Clientes con su teléfono. |
| `servicios` | Servicios con duración y precio. |
| `turnos` | Cada turno con inicio, fin, estado y precio acordado (se agregan fin y precio respecto al README). |
| `horarios` | Horario de atención por día de la semana. |
| `turnos.estado` | Valores: pendiente, confirmado, atendido, cancelado, no_vino |

## 12. Mensajes estándar

| Situación | Mensaje |
|---|---|
| Campo obligatorio vacío | Completá este campo |
| Número inválido | Ingresá un número válido |
| Monto cero o negativo | El monto debe ser mayor a 0 |
| Fecha inválida | Fecha no válida |
| Guardado correcto | Guardado |
| Error al guardar | No se pudo guardar. Intentá de nuevo |
| Confirmar borrado | ¿Borrar «{nombre}»? No se puede deshacer |
| Lista vacía | Todavía no hay nada acá. Tocá «+» para empezar |

## 13. Trazabilidad

| Historia | Fase | Casos de prueba |
|---|---|---|
| HU-01 Definir horario de atención | 2 | CP-01-1, CP-01-2 |
| HU-02 Clientes y servicios | 2 | CP-02-1, CP-02-2 |
| HU-03 Agendar un turno | 3 | CP-03-1, CP-03-2 |
| HU-04 Ver agenda diaria y semanal | 3 | CP-04-1, CP-04-2 |
| HU-05 Recordatorios y confirmación por WhatsApp | 4 | CP-05-1, CP-05-2 |
| HU-06 Resumen del día | 5 | CP-06-1 |

## 14. Documentos relacionados

- [DATOS.md](DATOS.md) · [PANTALLAS.md](PANTALLAS.md) · [PRUEBAS.md](PRUEBAS.md) · [BACKLOG.md](BACKLOG.md) · [ROADMAP.md](ROADMAP.md) · [PLANNING.md](PLANNING.md)
