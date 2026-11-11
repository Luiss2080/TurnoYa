# Modelo de datos — TurnoYa

> Diseño detallado de la base SQLite: campos, validaciones, consultas y migraciones. Complementa la sección 6 de [SPEC.md](SPEC.md).

## 1. Convenciones

- Base local `SQLite` con `expo-sqlite`; versión del esquema en `PRAGMA user_version`.
- Claves primarias `INTEGER AUTOINCREMENT` llamadas `id`.
- Fechas como texto ISO `AAAA-MM-DD` (y `AAAA-MM-DDTHH:MM` cuando hay hora). Nunca se guardan textos como «hoy».
- Montos como `REAL` en bolivianos; se redondean a 2 decimales al guardar y se formatean al mostrar.
- Booleanos como `INTEGER` 0/1.
- Los textos se guardan sin espacios sobrantes (`trim()`).
- Todas las consultas usan parámetros (`?`), nunca concatenan texto del usuario.
- Las claves foráneas se activan con `PRAGMA foreign_keys = ON` al abrir la base.

## 2. Relaciones

| Campo | Apunta a | Comportamiento |
|---|---|---|
| `turnos.cliente_id` | `clientes` | Sin borrado en cascada |
| `turnos.servicio_id` | `servicios` | Sin borrado en cascada |

```
clientes  ──<  turnos
servicios  ──<  turnos
turnos
horarios
```

## 3. Detalle por tabla

### 3.1 `clientes`

Clientes con su teléfono.

| Campo | Tipo SQL → TS | Control en formulario | Validación | Mensaje de error |
|---|---|---|---|---|
| `id` | INTEGER | Automático | Clave primaria | — |
| `nombre` | TEXT → `string` | Texto | Obligatorio; Texto de 1 a 80 caracteres sin espacios sobrantes | «Escribí nombre» |
| `telefono` | TEXT → `string \| null` | Teléfono | Opcional; Solo dígitos, 7 a 15 caracteres (se ignoran espacios y guiones) | «Teléfono no válido» |

**Consultas base** (en `src/db/clientes.ts`):

```ts
listar(): Promise<Cliente[]>            // SELECT * FROM clientes ORDER BY id DESC
obtener(id: number): Promise<Cliente | null>   // SELECT * FROM clientes WHERE id = ?
crear(datos: NuevoCliente): Promise<number>   // INSERT INTO clientes (nombre, telefono) VALUES (?, ?)
actualizar(id: number, datos: NuevoCliente): Promise<void>   // UPDATE clientes SET nombre = ?, telefono = ? WHERE id = ?
eliminar(id: number): Promise<void>      // DELETE FROM clientes WHERE id = ?
```

### 3.2 `servicios`

Servicios con duración y precio.

| Campo | Tipo SQL → TS | Control en formulario | Validación | Mensaje de error |
|---|---|---|---|---|
| `id` | INTEGER | Automático | Clave primaria | — |
| `nombre` | TEXT → `string` | Texto | Obligatorio; Texto de 1 a 80 caracteres sin espacios sobrantes | «Escribí nombre» |
| `minutos` | INTEGER → `number` | Numérico entero | Obligatorio; Entero mayor o igual a 0 | «Ingresá un número entero» |
| `precio` | REAL → `number` | Numérico decimal | Obligatorio; Mayor a 0; Hasta 2 decimales; Valor inicial `0` | «Ingresá un monto mayor a 0» |
| `activo` | INTEGER → `number` | Interruptor | Obligatorio; 0 o 1; Valor inicial `1` | — |

**Consultas base** (en `src/db/servicios.ts`):

```ts
listar(): Promise<Servicio[]>            // SELECT * FROM servicios ORDER BY id DESC
obtener(id: number): Promise<Servicio | null>   // SELECT * FROM servicios WHERE id = ?
crear(datos: NuevoServicio): Promise<number>   // INSERT INTO servicios (nombre, minutos, precio, activo) VALUES (?, ?, ?, ?)
actualizar(id: number, datos: NuevoServicio): Promise<void>   // UPDATE servicios SET nombre = ?, minutos = ?, precio = ?, activo = ? WHERE id = ?
eliminar(id: number): Promise<void>      // DELETE FROM servicios WHERE id = ?
```

### 3.3 `turnos`

Cada turno con inicio, fin, estado y precio acordado (se agregan fin y precio respecto al README).

| Campo | Tipo SQL → TS | Control en formulario | Validación | Mensaje de error |
|---|---|---|---|---|
| `id` | INTEGER | Automático | Clave primaria | — |
| `cliente_id` | INTEGER → `number` | Selector de clientes | Obligatorio; Debe existir en `clientes` | «Elegí un registro» |
| `servicio_id` | INTEGER → `number` | Selector de servicios | Obligatorio; Debe existir en `servicios` | «Elegí un registro» |
| `inicio` | TEXT → `string` | Fecha | Obligatorio; Fecha real en formato AAAA-MM-DD | «Fecha no válida» |
| `fin` | TEXT → `string` | Fecha | Obligatorio; Fecha real en formato AAAA-MM-DD; No anterior a la fecha de inicio/aplicación si ambas existen | «Fecha no válida» |
| `estado` | TEXT → `string` | Selector / fichas | Obligatorio; Uno de: `pendiente`, `confirmado`, `atendido`, `cancelado`, `no_vino`; Valor inicial `pendiente` | «Elegí una opción» |
| `precio` | REAL → `number` | Numérico decimal | Obligatorio; Mayor a 0; Hasta 2 decimales; Valor inicial `0` | «Ingresá un monto mayor a 0» |

**Índices recomendados** (agregarlos en la migración si aún no existen):

```sql
CREATE INDEX IF NOT EXISTS idx_turnos_cliente_id ON turnos(cliente_id);
CREATE INDEX IF NOT EXISTS idx_turnos_servicio_id ON turnos(servicio_id);
CREATE INDEX IF NOT EXISTS idx_turnos_inicio ON turnos(inicio);
CREATE INDEX IF NOT EXISTS idx_turnos_fin ON turnos(fin);
CREATE INDEX IF NOT EXISTS idx_turnos_estado ON turnos(estado);
```

**Consultas base** (en `src/db/turnos.ts`):

```ts
listar(): Promise<Turno[]>            // SELECT * FROM turnos ORDER BY id DESC
obtener(id: number): Promise<Turno | null>   // SELECT * FROM turnos WHERE id = ?
crear(datos: NuevoTurno): Promise<number>   // INSERT INTO turnos (cliente_id, servicio_id, inicio, fin, estado, precio) VALUES (?, ?, ?, ?, ?, ?)
actualizar(id: number, datos: NuevoTurno): Promise<void>   // UPDATE turnos SET cliente_id = ?, servicio_id = ?, inicio = ?, fin = ?, estado = ?, precio = ? WHERE id = ?
eliminar(id: number): Promise<void>      // DELETE FROM turnos WHERE id = ?
listarPorCliente(cliente_id: number): Promise<Turno[]>   // WHERE cliente_id = ?
listarPorServicio(servicio_id: number): Promise<Turno[]>   // WHERE servicio_id = ?
```

### 3.4 `horarios`

Horario de atención por día de la semana.

| Campo | Tipo SQL → TS | Control en formulario | Validación | Mensaje de error |
|---|---|---|---|---|
| `id` | INTEGER | Automático | Clave primaria | — |
| `dia_semana` | INTEGER → `number` | Numérico entero | Obligatorio; Entero mayor o igual a 0 | «Ingresá un número entero» |
| `desde` | TEXT → `string` | Fecha | Obligatorio; Fecha real en formato AAAA-MM-DD | «Fecha no válida» |
| `hasta` | TEXT → `string` | Fecha | Obligatorio; Fecha real en formato AAAA-MM-DD | «Fecha no válida» |

**Consultas base** (en `src/db/horarios.ts`):

```ts
listar(): Promise<Horario[]>            // SELECT * FROM horarios ORDER BY id DESC
obtener(id: number): Promise<Horario | null>   // SELECT * FROM horarios WHERE id = ?
crear(datos: NuevoHorario): Promise<number>   // INSERT INTO horarios (dia_semana, desde, hasta) VALUES (?, ?, ?)
actualizar(id: number, datos: NuevoHorario): Promise<void>   // UPDATE horarios SET dia_semana = ?, desde = ?, hasta = ? WHERE id = ?
eliminar(id: number): Promise<void>      // DELETE FROM horarios WHERE id = ?
```

## 4. Tipos TypeScript

Copiar a `src/db/tipos.ts`:

```ts
export interface Cliente {
  id: number;
  nombre: string;
  telefono: string | null;
}
export type NuevoCliente = Omit<Cliente, 'id'>;

export interface Servicio {
  id: number;
  nombre: string;
  minutos: number;
  precio: number;
  activo: number;
}
export type NuevoServicio = Omit<Servicio, 'id'>;

export interface Turno {
  id: number;
  cliente_id: number;
  servicio_id: number;
  inicio: string;
  fin: string;
  estado: string;
  precio: number;
}
export type NuevoTurno = Omit<Turno, 'id'>;

export interface Horario {
  id: number;
  dia_semana: number;
  desde: string;
  hasta: string;
}
export type NuevoHorario = Omit<Horario, 'id'>;

```

## 5. Valores permitidos (enumeraciones)

SQLite no tiene tipo enumerado: se valida en la capa de datos y se muestra con la etiqueta de la tabla.

**`turnos.estado`**

| Valor guardado | Etiqueta en pantalla |
|---|---|
| `pendiente` | Pendiente |
| `confirmado` | Confirmado |
| `atendido` | Atendido |
| `cancelado` | Cancelado |
| `no_vino` | No vino |

```ts
export const TURNOS_ESTADO = ['pendiente', 'confirmado', 'atendido', 'cancelado', 'no_vino'] as const;
```

## 6. Migraciones

Migración 1 = esquema inicial (ya en `src/db/schema.ts` tras la fase 1). Cada cambio posterior es una migración nueva al final de la lista; nunca se edita una ya publicada.

| Versión | Fase | Motivo |
|---|---|---|
| 2 | 4 | Minutos de aviso y plantilla de confirmación. |

**Migración 2** (fase 4)

```sql
CREATE TABLE ajustes (clave TEXT PRIMARY KEY, valor TEXT NOT NULL);
```

## 7. Esquema inicial completo

```sql
CREATE TABLE clientes (id INTEGER PRIMARY KEY AUTOINCREMENT, nombre TEXT NOT NULL, telefono TEXT);
CREATE TABLE servicios (id INTEGER PRIMARY KEY AUTOINCREMENT, nombre TEXT NOT NULL, minutos INTEGER NOT NULL, precio REAL NOT NULL DEFAULT 0, activo INTEGER NOT NULL DEFAULT 1);
CREATE TABLE turnos (id INTEGER PRIMARY KEY AUTOINCREMENT, cliente_id INTEGER NOT NULL REFERENCES clientes(id), servicio_id INTEGER NOT NULL REFERENCES servicios(id), inicio TEXT NOT NULL, fin TEXT NOT NULL, estado TEXT NOT NULL DEFAULT 'pendiente', precio REAL NOT NULL DEFAULT 0);
CREATE TABLE horarios (id INTEGER PRIMARY KEY AUTOINCREMENT, dia_semana INTEGER NOT NULL, desde TEXT NOT NULL, hasta TEXT NOT NULL);
```

## 8. Reglas de integridad que NO delega a la base

- **RN-01** El fin de un turno = inicio + duración del servicio (queda guardado en `fin`).
- **RN-02** Dos turnos no pueden superponerse; el choque se valida al guardar y al mover.
- **RN-03** Solo se pueden agendar turnos dentro del horario de atención del día.
- **RN-04** Estados del turno: pendiente, confirmado, atendido, cancelado y no vino.
- **RN-05** El precio del turno se copia del servicio al agendar y no cambia si el servicio cambia después.
- **RN-06** Los ingresos del día cuentan los turnos atendidos.
- **RN-07** El recordatorio local se programa antes del inicio según los minutos configurados y se cancela si el turno se cancela.

Estas reglas se implementan en `src/lib/` como funciones puras y se prueban de forma automática (ver [PRUEBAS.md](PRUEBAS.md)).
