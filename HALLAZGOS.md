# Hallazgos

Aquí van **las tres líneas** del ejercicio, una por cada punto de abajo. Es lo único que hay que
traer hecho: un cambio de motor a medias con estas tres líneas escritas vale más que lo contrario,
porque lo que se discute en el directo es dónde te chocaste.

Escribe **una sola línea por punto**, con tus palabras y con lo que mediste, no con lo que suponías.

## 1. Las filas que cambian y la rama

Cuántas filas cambian de valor en tu cambio de esquema, medido con una consulta, y en qué rama del
árbol de reversibilidad cae. Si tu migración no toca datos, dilo tal cual: también es una respuesta.

- 0 filas cambian de valor: no se tocó ninguna migración (`git diff` vacío) ni se copiaron datos, las bases se migran desde cero; cae en la rama de lo reversible (volver a SQLite es revertir la configuración). Pero el cambio de motor sí cambia cómo se lee `due_date`: `select count(*) from tasks where due_date < date '2026-10-07' and status <> 'done'` → 1, y esa tarea sale con `isOverdue: false`.

## 2. Lo que la batería de pruebas no podía ver

Una cosa que la batería de pruebas no podía ver. Si no encontraste ninguna, escribe qué buscaste y dónde.

- `isOverdue` es siempre `false` en PostgreSQL: el driver `pg` devuelve las columnas `DATE` como `Date` de JS y no como texto, así que `this.dueDate < referenceDay` compara un `Date` con un string y da `false`. Tarea con fecha 2026-01-15 consultada el 2026-10-07: el PUT responde `true` (valor en memoria) y el GET `false` con `dueDate: "2026-01-15T00:00:00.000Z"`. Typecheck en verde y `schema.ts` sin diff porque `schema_rules.ts` fija `dueDate` como `string`; el comentario de `isOverdueOn` («texto contra texto») ya no es verdad. Ningún test lee una fecha de vuelta de la base.

## 3. Tu duda

De qué dudaste, o qué no pudiste comprobar.

- Mi duda fue dónde estaba el fallo: si en PostgreSQL, en las migraciones o en los datos. Resultó que en ninguno: los datos y las migraciones están bien, y es el driver `pg` el que convierte `DATE` en `Date` de JS. Dejé el arreglo (`pg.types.setTypeParser(1082, v => v)`) sin aplicar para el directo. Medí además que con `TZ=Europe/Madrid` la fecha `2026-01-15` llega como `2026-01-14T23:00:00.000Z` (un día antes). No comprobé si `createdAt`/`updatedAt` se comportan distinto que en SQLite.
