# Prompts

Aquí van **todos los prompts que lanzaste** para hacer el ejercicio, en el orden en que los
lanzaste, con el modelo y la herramienta de cada uno.

Esto no es papeleo. Lo que se revisa es **cómo pediste las cosas**, no solo lo que salió: un
resultado flojo con un prompt bueno y un resultado flojo con un prompt vago necesitan feedback
distinto, y sin este archivo no se distinguen.

## Cómo rellenarlo

- Un apartado `## Prompt N` por cada prompt.
- **Pega el prompt tal cual lo lanzaste**, dentro del bloque de código, aunque ocupe diez líneas
y aunque tenga faltas. No lo reescribas para que quede bien: el que arreglaste mentalmente
después no es el que lanzaste.
- Incluye también los que **no funcionaron**. Suelen ser los más útiles de leer.
- `Modelo` y `Herramienta` en todos. Si cambiaste de una a otra a mitad, se nota aquí.

Borra el ejemplo de abajo cuando escribas el primero.

---



## Prompt 1

**Modelo:** Claude Opus 5.5
**Herramienta:** Claude Code 

```
Migra el backend de SQLite a PostgreSQL en Docker, con dos bases: una de desarrollo y otra de pruebas.

Restricciones (no negociables):
- Fichero de Compose en la raíz llamado compose.yaml, servicios `db` y `db-test`, sin la clave `version:`.
- Imagen pgvector/pgvector:pg17 en los dos.
- Puertos 54410 (db) y 54411 (db-test). No uses el 5432.
- db lleva volumen; db-test va en memoria, sin volumen (efímera a propósito).
- Los dos servicios con healthcheck, y el arranque espera a que estén sanos (la imagen no acepta conexiones mientras crea la base la primera vez).
- La suite de tests apunta a db-test mediante backend/.env.test, que el framework carga solo con NODE_ENV=test.
- Atajos en el Makefile para levantar las bases, pararlas, migrar LAS DOS y correr los tests.
- NO toques ninguna migración existente. Si crees que hace falta, para y dímelo antes.

Cuando termines: baja y sube las bases, migra y corre los tests, y enséñame el resultado. No des el trabajo por terminado solo porque los tests estén en verde.
```

**Qué salió:** cumplió las restricciones y, sin pedírselo, probó contra la API real y encontró que `isOverdue` sale siempre `false` al leer de PostgreSQL. También hizo cosas que no pedí: tocó el CI (`openapi.yml`), hizo que `make clean` borre el volumen de `db` y desactivó `fsync` en `db-test`.

## Prompt 2

**Modelo:** Claude Opus 5.5
**Herramienta:** Claude Code 

**Contexto:** el agente terminó preguntando si arreglaba `isOverdue` indicándole al driver que devuelva los `DATE` como texto (`pg.types.setTypeParser(1082, v => v)` en `config/database.ts`), o si lo dejaba así para que yo lo investigara y lo escribiera en `HALLAZGOS.md`.

```
No lo apliques, lo dejo como hallazgo. Actualiza CLAUDE.md, docs/architecture.md y el README de tasks para que no hablen de SQLite, y no commitees todavía.
```

**Qué salió:** actualizó los tres documentos y avisó de un comentario de `signup.spec.ts` que seguía hablando de SQLite.

## Prompt 3

**Modelo:** Claude Opus 5.5
**Herramienta:** Claude Code

**Contexto:** el agente avisó de que el comentario de cabecera de `backend/tests/functional/auth/signup.spec.ts` seguía diciendo que los tests pegan contra el mismo fichero SQLite que el servidor de desarrollo, y preguntó si lo actualizaba.

```
Sí, actualízalo. Solo el comentario, no cambies el test.
```
