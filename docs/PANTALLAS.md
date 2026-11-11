# Pantallas y flujos — TurnoYa

> Guía de interfaz para construir cada pantalla: ruta, contenido, estados y bosquejo. Complementa la sección 5 de [SPEC.md](SPEC.md).

## 1. Mapa de rutas

| Pantalla | Archivo (Expo Router) | Tipo |
|---|---|---|
| Inicio | `app/index.tsx` | Inicio |
| Agenda | `app/agenda.tsx` | Lista / vista |
| Formulario de turno | `app/formulario-de-turno.tsx` | Formulario |
| Clientes | `app/clientes.tsx` | Lista / vista |
| Servicios | `app/servicios.tsx` | Lista / vista |
| Horarios | `app/horarios.tsx` | Lista / vista |
| Resumen del día | `app/resumen-del-dia.tsx` | Detalle |

**Convenciones de interfaz**

- Una acción principal por pantalla, en un botón grande abajo o en un botón flotante.
- Los formularios se abren como pantalla completa, validan al enviar y muestran el error junto al campo.
- Toda lista tiene estado vacío con una frase y un botón que lleva a la acción.
- Las acciones que borran piden confirmación explícita con el nombre del registro.
- El botón atrás nunca pierde datos sin avisar: si el formulario tiene cambios, se pregunta antes de salir.
- Textos en español de Bolivia (voseo opcional, siempre claros); montos como `Bs 1.250,00`.

## 2. Detalle por pantalla

### 2.1 Inicio

- **Archivo:** `app/index.tsx`
- **Qué muestra:** Turnos de hoy y próximo turno
- **Acciones:** Nuevo turno

**Bosquejo**

```
┌──────────────────────────────┐
│ TurnoYa                      │
├──────────────────────────────┤
│ [ Resumen y alertas ]        │
│                              │
│  ▸ Agenda                   │
│  ▸ Clientes                 │
│  ▸ Servicios                │
│  ▸ Horarios                 │
│                              │
└──────────────────────────────┘
```

**Estados**

| Estado | Qué ve el usuario |
|---|---|
| Cargando | Indicador breve; la base es local, rara vez se nota |
| Vacío | Todavía no hay información para mostrar. |
| Con datos | Turnos de hoy y próximo turno |
| Error | Mensaje «No se pudo guardar/cargar. Intentá de nuevo» con botón de reintento; el error técnico va a la consola |
| Sin cambios (formularios) | El botón guardar queda deshabilitado hasta que haya cambios válidos |

**Accesibilidad:** cada botón con `accessibilityLabel`, áreas táctiles de 48 px o más, no depender solo del color para indicar estado.

### 2.2 Agenda

- **Archivo:** `app/agenda.tsx`
- **Qué muestra:** Vista día y semana con huecos libres
- **Acciones:** Tocar hueco para agendar, mover turno
- **Historias relacionadas:** HU-03, HU-04, HU-06 (sugerido por coincidencia de temas; confirmar al construir).

**Bosquejo**

```
┌──────────────────────────────┐
│ ←  Agenda                      │
├──────────────────────────────┤
│ nombre · fecha               │
│ nombre · fecha               │
│ nombre · fecha               │
│                              │
│                         (+)  │
└──────────────────────────────┘
```

**Estados**

| Estado | Qué ve el usuario |
|---|---|
| Cargando | Indicador breve; la base es local, rara vez se nota |
| Vacío | Todavía no hay información para mostrar. |
| Con datos | Vista día y semana con huecos libres |
| Error | Mensaje «No se pudo guardar/cargar. Intentá de nuevo» con botón de reintento; el error técnico va a la consola |
| Sin cambios (formularios) | El botón guardar queda deshabilitado hasta que haya cambios válidos |

**Accesibilidad:** cada botón con `accessibilityLabel`, áreas táctiles de 48 px o más, no depender solo del color para indicar estado.

### 2.3 Formulario de turno

- **Archivo:** `app/formulario-de-turno.tsx`
- **Qué muestra:** Cliente, servicio, fecha, hora (solo libres)
- **Acciones:** Guardar, cancelar turno
- **Historias relacionadas:** HU-02, HU-03, HU-04, HU-06 (sugerido por coincidencia de temas; confirmar al construir).

**Campos del formulario**

| Campo | Control | Obligatorio | Validación |
|---|---|---|---|
| cliente id | Selector de clientes | Sí | Debe existir en `clientes` |
| servicio id | Selector de servicios | Sí | Debe existir en `servicios` |
| inicio | Fecha | Sí | Fecha real en formato AAAA-MM-DD |
| fin | Fecha | Sí | Fecha real en formato AAAA-MM-DD; No anterior a la fecha de inicio/aplicación si ambas existen |
| estado | Selector / fichas | Sí | Uno de: `pendiente`, `confirmado`, `atendido`, `cancelado`, `no_vino` |
| precio | Numérico decimal | Sí | Mayor a 0; Hasta 2 decimales |

**Bosquejo**

```
┌──────────────────────────────┐
│ ←  Formulario de turno         │
├──────────────────────────────┤
│ cliente id                   │
│ [____________________]       │
│ servicio id                  │
│ [____________________]       │
│ inicio                       │
│ [____________________]       │
│ fin                          │
│ [____________________]       │
│ estado                       │
│ [____________________]       │
│                              │
│ [        Guardar        ]    │
└──────────────────────────────┘
```

**Estados**

| Estado | Qué ve el usuario |
|---|---|
| Cargando | Indicador breve; la base es local, rara vez se nota |
| Vacío | Todavía no hay turnos. Tocá «Guardar» para empezar. |
| Con datos | Cliente, servicio, fecha, hora (solo libres) |
| Error | Mensaje «No se pudo guardar/cargar. Intentá de nuevo» con botón de reintento; el error técnico va a la consola |
| Sin cambios (formularios) | El botón guardar queda deshabilitado hasta que haya cambios válidos |

**Accesibilidad:** cada botón con `accessibilityLabel`, áreas táctiles de 48 px o más, no depender solo del color para indicar estado.

### 2.4 Clientes

- **Archivo:** `app/clientes.tsx`
- **Qué muestra:** Lista con búsqueda
- **Acciones:** Crear, editar
- **Historias relacionadas:** HU-02, HU-03 (sugerido por coincidencia de temas; confirmar al construir).

**Bosquejo**

```
┌──────────────────────────────┐
│ ←  Clientes                    │
├──────────────────────────────┤
│ nombre · fecha               │
│ nombre · fecha               │
│ nombre · fecha               │
│                              │
│                         (+)  │
└──────────────────────────────┘
```

**Estados**

| Estado | Qué ve el usuario |
|---|---|
| Cargando | Indicador breve; la base es local, rara vez se nota |
| Vacío | Todavía no hay clientes. Tocá «Crear» para empezar. |
| Con datos | Lista con búsqueda |
| Error | Mensaje «No se pudo guardar/cargar. Intentá de nuevo» con botón de reintento; el error técnico va a la consola |
| Sin cambios (formularios) | El botón guardar queda deshabilitado hasta que haya cambios válidos |

**Accesibilidad:** cada botón con `accessibilityLabel`, áreas táctiles de 48 px o más, no depender solo del color para indicar estado.

### 2.5 Servicios

- **Archivo:** `app/servicios.tsx`
- **Qué muestra:** Lista con duración y precio
- **Acciones:** Crear, editar, desactivar
- **Historias relacionadas:** HU-02, HU-03 (sugerido por coincidencia de temas; confirmar al construir).

**Bosquejo**

```
┌──────────────────────────────┐
│ ←  Servicios                   │
├──────────────────────────────┤
│ nombre · minutos             │
│ nombre · minutos             │
│ nombre · minutos             │
│                              │
│                         (+)  │
└──────────────────────────────┘
```

**Estados**

| Estado | Qué ve el usuario |
|---|---|
| Cargando | Indicador breve; la base es local, rara vez se nota |
| Vacío | Todavía no hay servicios. Tocá «Crear» para empezar. |
| Con datos | Lista con duración y precio |
| Error | Mensaje «No se pudo guardar/cargar. Intentá de nuevo» con botón de reintento; el error técnico va a la consola |
| Sin cambios (formularios) | El botón guardar queda deshabilitado hasta que haya cambios válidos |

**Accesibilidad:** cada botón con `accessibilityLabel`, áreas táctiles de 48 px o más, no depender solo del color para indicar estado.

### 2.6 Horarios

- **Archivo:** `app/horarios.tsx`
- **Qué muestra:** Horario por día de la semana
- **Acciones:** Guardar
- **Historias relacionadas:** HU-01, HU-04, HU-06 (sugerido por coincidencia de temas; confirmar al construir).

**Bosquejo**

```
┌──────────────────────────────┐
│ ←  Horarios                    │
├──────────────────────────────┤
│ desde · dia_semana           │
│ desde · dia_semana           │
│ desde · dia_semana           │
│                              │
│                         (+)  │
└──────────────────────────────┘
```

**Estados**

| Estado | Qué ve el usuario |
|---|---|
| Cargando | Indicador breve; la base es local, rara vez se nota |
| Vacío | Todavía no hay horarios. Tocá «Guardar» para empezar. |
| Con datos | Horario por día de la semana |
| Error | Mensaje «No se pudo guardar/cargar. Intentá de nuevo» con botón de reintento; el error técnico va a la consola |
| Sin cambios (formularios) | El botón guardar queda deshabilitado hasta que haya cambios válidos |

**Accesibilidad:** cada botón con `accessibilityLabel`, áreas táctiles de 48 px o más, no depender solo del color para indicar estado.

### 2.7 Resumen del día

- **Archivo:** `app/resumen-del-dia.tsx`
- **Qué muestra:** Turnos por estado e ingresos
- **Acciones:** Cambiar fecha, exportar
- **Historias relacionadas:** HU-03, HU-04, HU-06 (sugerido por coincidencia de temas; confirmar al construir).

**Bosquejo**

```
┌──────────────────────────────┐
│ ←  Resumen del día             │
├──────────────────────────────┤
│ nombre                       │
│ Resumen / totales            │
├──────────────────────────────┤
│ • registro 1                 │
│ • registro 2                 │
│ • registro 3                 │
│                         (+)  │
└──────────────────────────────┘
```

**Estados**

| Estado | Qué ve el usuario |
|---|---|
| Cargando | Indicador breve; la base es local, rara vez se nota |
| Vacío | Todavía no hay información para mostrar. |
| Con datos | Turnos por estado e ingresos |
| Error | Mensaje «No se pudo guardar/cargar. Intentá de nuevo» con botón de reintento; el error técnico va a la consola |
| Sin cambios (formularios) | El botón guardar queda deshabilitado hasta que haya cambios válidos |

**Accesibilidad:** cada botón con `accessibilityLabel`, áreas táctiles de 48 px o más, no depender solo del color para indicar estado.

## 3. Flujos principales

**HU-01 — Definir horario de atención**

```
abro la agenda del domingo  →  veo el día como cerrado
```

**HU-02 — Clientes y servicios**

```
lo guardo  →  queda disponible al agendar
```

**HU-03 — Agendar un turno**

```
agendo un servicio de 45 minutos  →  el turno ocupa el bloque de 45 minutos en la agenda
```

**HU-04 — Ver agenda diaria y semanal**

```
abro la agenda  →  veo los turnos y los huecos libres en orden
```

**HU-05 — Recordatorios y confirmación por WhatsApp**

```
toco «Confirmar por WhatsApp»  →  se abre WhatsApp con el mensaje prellenado con fecha, hora y servicio
```

**HU-06 — Resumen del día**

```
abro Resumen  →  veo cantidades y el ingreso total de los atendidos
```
