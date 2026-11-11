# Plan de pruebas — TurnoYa

> Qué probar, cuándo y cómo. Cada historia y cada regla de negocio tiene casos concretos. Complementa a [SPEC.md](SPEC.md).

## 1. Estrategia

| Nivel | Herramienta | Qué cubre | Cuándo |
|---|---|---|---|
| Unitarias | jest-expo (`npm test`) | Lógica pura de `src/lib/` (cálculos, validaciones, formatos) | En cada commit (CI) |
| Manuales por historia | Teléfono con Expo Go | Criterios de aceptación de SPEC (casos CP-) | Al cerrar cada tarea de pantalla |
| Regresión por fase | Teléfono real | Lo ya terminado sigue andando | Al cerrar cada fase |
| Aceptación final | APK en teléfono limpio, modo avión | Criterios de lanzamiento | Antes de v1.0.0 |

Meta de cobertura: **100 % de las reglas de negocio** con prueba automática o caso manual, y **100 % de los criterios de aceptación** ejecutados al menos una vez en un teléfono real.

## 2. Pruebas unitarias por regla de negocio

Crear un archivo `src/lib/<tema>.test.ts` por tema. Una prueba por regla como mínimo; agregar los bordes de la sección 4.

| ID | Regla | Tipo de prueba | Casos mínimos |
|---|---|---|---|
| RN-01 | El fin de un turno = inicio + duración del servicio (queda guardado en `fin`). | Unitaria o manual | Caso válido, caso inválido, límite |
| RN-02 | Dos turnos no pueden superponerse; el choque se valida al guardar y al mover. | Unitaria o manual | Caso válido, caso inválido, límite |
| RN-03 | Solo se pueden agendar turnos dentro del horario de atención del día. | Unitaria (fechas) | Hoy, mañana, fin de mes, año bisiesto |
| RN-04 | Estados del turno: pendiente, confirmado, atendido, cancelado y no vino. | Unitaria o manual | Caso válido, caso inválido, límite |
| RN-05 | El precio del turno se copia del servicio al agendar y no cambia si el servicio cambia después. | Unitaria o manual | Caso válido, caso inválido, límite |
| RN-06 | Los ingresos del día cuentan los turnos atendidos. | Unitaria (fechas) | Hoy, mañana, fin de mes, año bisiesto |
| RN-07 | El recordatorio local se programa antes del inicio según los minutos configurados y se cancela si el turno se cancela. | Unitaria o manual | Caso válido, caso inválido, límite |

## 3. Casos manuales por historia

Formato: ejecutar en el teléfono, marcar ✔ o ✘ y anotar la fecha. Si falla, abrir un issue con el ID del caso.

### HU-01 — Definir horario de atención (fase 2)

| ID | Dado | Cuando | Entonces | Resultado |
|---|---|---|---|---|
| CP-01-1 | un horario lunes a sábado de 9 a 18 | abro la agenda del domingo | veo el día como cerrado | ☐ |
| CP-01-2 | un horario con «hasta» anterior a «desde» | intento guardar | la app lo rechaza | ☐ |

Casos transversales: **CP-01-T1** modo avión · **CP-01-T2** cerrar y reabrir la app (persistencia) · **CP-01-T3** texto del sistema en tamaño grande.

### HU-02 — Clientes y servicios (fase 2)

| ID | Dado | Cuando | Entonces | Resultado |
|---|---|---|---|---|
| CP-02-1 | un servicio de 45 minutos y 60 Bs | lo guardo | queda disponible al agendar | ☐ |
| CP-02-2 | un cliente sin teléfono | lo guardo | se guarda pero no podré confirmar por WhatsApp | ☐ |

Casos transversales: **CP-02-T1** modo avión · **CP-02-T2** cerrar y reabrir la app (persistencia) · **CP-02-T3** texto del sistema en tamaño grande.

### HU-03 — Agendar un turno (fase 3)

| ID | Dado | Cuando | Entonces | Resultado |
|---|---|---|---|---|
| CP-03-1 | un horario libre | agendo un servicio de 45 minutos | el turno ocupa el bloque de 45 minutos en la agenda | ☐ |
| CP-03-2 | un horario ocupado | intento agendar encima | la app lo bloquea y propone el próximo horario libre | ☐ |

Casos transversales: **CP-03-T1** modo avión · **CP-03-T2** cerrar y reabrir la app (persistencia) · **CP-03-T3** texto del sistema en tamaño grande.

### HU-04 — Ver agenda diaria y semanal (fase 3)

| ID | Dado | Cuando | Entonces | Resultado |
|---|---|---|---|---|
| CP-04-1 | un día con 6 turnos | abro la agenda | veo los turnos y los huecos libres en orden | ☐ |
| CP-04-2 | la vista semanal | la abro | veo la carga por día | ☐ |

Casos transversales: **CP-04-T1** modo avión · **CP-04-T2** cerrar y reabrir la app (persistencia) · **CP-04-T3** texto del sistema en tamaño grande.

### HU-05 — Recordatorios y confirmación por WhatsApp (fase 4)

| ID | Dado | Cuando | Entonces | Resultado |
|---|---|---|---|---|
| CP-05-1 | un turno mañana a las 10:00 | toco «Confirmar por WhatsApp» | se abre WhatsApp con el mensaje prellenado con fecha, hora y servicio | ☐ |
| CP-05-2 | un turno en 60 minutos | llega la hora del aviso | recibo una notificación local | ☐ |

Casos transversales: **CP-05-T1** modo avión · **CP-05-T2** cerrar y reabrir la app (persistencia) · **CP-05-T3** texto del sistema en tamaño grande.

### HU-06 — Resumen del día (fase 5)

| ID | Dado | Cuando | Entonces | Resultado |
|---|---|---|---|---|
| CP-06-1 | un día con 8 atendidos y 1 que no vino | abro Resumen | veo cantidades y el ingreso total de los atendidos | ☐ |

Casos transversales: **CP-06-T1** modo avión · **CP-06-T2** cerrar y reabrir la app (persistencia) · **CP-06-T3** texto del sistema en tamaño grande.

## 4. Casos borde

| ID | Caso | Resultado esperado | Resultado |
|---|---|---|---|
| CB-01 | Turno que cruza la hora de cierre: no se permite. | La app no se rompe; el mensaje o comportamiento es claro y no se pierden datos | ☐ |
| CB-02 | Cambio de la duración de un servicio: no modifica turnos ya agendados. | La app no se rompe; el mensaje o comportamiento es claro y no se pierden datos | ☐ |
| CB-03 | Cliente repetido por teléfono: avisar y ofrecer usar el existente. | La app no se rompe; el mensaje o comportamiento es claro y no se pierden datos | ☐ |
| CB-04 | Cambio de horario de atención con turnos ya agendados fuera del nuevo horario: se muestran como advertencia. | La app no se rompe; el mensaje o comportamiento es claro y no se pierden datos | ☐ |
| CB-05 | Cambio de hora del teléfono por horario de verano o zona: los turnos se guardan en hora local ISO sin zona. | La app no se rompe; el mensaje o comportamiento es claro y no se pierden datos | ☐ |

## 5. Pruebas de datos y migraciones

| ID | Prueba | Esperado |
|---|---|---|
| CD-01 | Primer arranque en teléfono limpio | La base se crea con todas las tablas y `user_version` queda en 1 |
| CD-02 | Actualizar de una versión anterior con datos cargados | Las migraciones nuevas se aplican y no se pierde ningún registro |
| CD-03 | Borrar un registro padre | Los hijos se borran o se bloquea con mensaje, según el esquema |
| CD-04 | Intentar guardar con campos obligatorios vacíos | No se inserta nada y se marcan los campos |
| CD-05 | Cargar 1.000 registros de prueba | Las listas siguen fluidas (scroll sin trabas) y abren en menos de 2 s |

## 6. Regresión al cerrar cada fase

**Fase 1 — Estructura base y navegación**

- [ ] La app abre y navega sin errores

**Fase 2 — Clientes y servicios**

- [ ] Puedo configurar horario, 3 servicios y 10 clientes y verlos tras reiniciar.
- [ ] Los criterios de las fases anteriores siguen funcionando

**Fase 3 — Agenda y turnos**

- [ ] No es posible crear dos turnos superpuestos.
- [ ] Un turno que termina a las 10:00 y otro que empieza a las 10:00 no chocan.
- [ ] Los criterios de las fases anteriores siguen funcionando

**Fase 4 — Recordatorios y WhatsApp**

- [ ] Los recordatorios llegan a la hora correcta y se cancelan al cancelar el turno.
- [ ] Los criterios de las fases anteriores siguen funcionando

**Fase 5 — Reportes del día**

- [ ] El ingreso del día coincide con la suma de turnos atendidos.
- [ ] Los criterios de las fases anteriores siguen funcionando

## 7. Matriz de dispositivos

| Dispositivo | Android | Qué se verifica |
|---|---|---|
| Gama baja (2–3 GB de RAM) | 8–10 | Fluidez de listas, tiempo de arranque |
| Gama media | 11–13 | Flujo completo y APK |
| Pantalla pequeña (5") | cualquiera | Que nada quede cortado ni oculto por el teclado |
| Texto del sistema grande | cualquiera | Que los formularios sigan siendo usables |

## 8. Lista de verificación para el lanzamiento (v1.0.0)

- [ ] Todas las historias ejecutadas en teléfono real (casos CP-)
- [ ] Todos los casos borde (CB-) ejecutados
- [ ] Pruebas de datos (CD-) ejecutadas
- [ ] APK instalado en un teléfono limpio sin Expo Go
- [ ] Probado en modo avión durante todo el flujo principal
- [ ] Rotación de pantalla no pierde datos del formulario
- [ ] Permisos solicitados solo cuando se necesitan y con explicación
- [ ] Sin errores en la consola durante un recorrido completo
- [ ] README con capturas y CHANGELOG al día

## 9. Informe de errores

```
Título: [Bug] <qué pasó>
ID de caso: CP-XX-X
Pasos: 1... 2... 3...
Esperado: ...
Obtenido: ...
Dispositivo / Android: ...
Captura o video: ...
```
