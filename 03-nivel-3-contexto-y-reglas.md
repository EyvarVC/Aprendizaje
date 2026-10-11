# Nivel 3 - Contexto y reglas

## Objetivo
Que el agente funcione en TU repositorio sin explicarle la estructura cada vez.
Aqui es donde se gana o se pierde la productividad.

---

## 1. AGENTS.md: el archivo mas rentable de tu repo

### Que es
Un archivo de texto en la raiz del repo con las instrucciones que el agente lee
al arrancar. Distintos agentes usan nombres ligeramente distintos
(`AGENTS.md`, `CLAUDE.md`, `.cursorrules`), pero el concepto es el mismo:
**instrucciones persistentes del proyecto**.

### Por que es mas importante que el prompt
- Se carga en cada sesion, automaticamente.
- Sobrevive a la compaction (esta en disco).
- Se versiona: todos los agentes del equipo leen lo mismo.
- Es mas barato: escribir una vez en vez de explicar cada vez.

### Estructura recomendada
1. Que es el proyecto (2 lineas).
2. Stack exacto con versiones.
3. Comandos (install, test, lint, build, run).
4. Estructura de carpetas y responsabilidad de cada una.
5. Convenciones de codigo (con ejemplos, no solo reglas).
6. Que NO hacer.
7. Casos especiales.

### Que poner y que no
| SI | NO |
|---|---|
| Version exacta de la libreria | "Usa las mejores practicas" |
| `npm run test` | "Ejecuta los tests" |
| "No usamos `any`" (por que: strict) | "Escribe buen codigo" |
| Estructura con responsabilidades | Estructura sin explicar nada |
| Que NO hacer (ej: "no tocar migraciones") | El README reescrito |
| 1 ejemplo real de codigo correcto | Reglas sin ejemplo |

**Regla de oro:** un ejemplo de codigo real distila mas que diez reglas de prosa.

### Ejemplo concreto
```markdown
# Projecto: API de inventarios

## Stack
- Node 24.21.0, TypeScript 5.x (strict: true)
- Express 4, PostgreSQL via Prisma
- Tests: Vitest. NO usar Jest (deprecated en este repo).

## Comandos
- `npm install`
- `npm run dev` - servidor con hot reload
- `npm run test` - tests unitarios (Vitest)
- `npm run typecheck` - tsc --noEmit
- `npm run lint` - ESLint

## Estructura
- `src/routes/` - solo definicion de endpoints, sin logica de negocio
- `src/services/` - logica de negocio, recibe deps por parametro
- `src/db/` - Prisma client y queries, unica capa que toca la DB
- `src/middleware/` - auth, validacion, error handling
- `tests/` - espeja `src/`, un archivo de test por archivo fuente

## Convenciones
- Los services NO importan de `routes/`. Regla de dependencia en una direccion.
- Validacion con zod en el borde (routes), nunca en el service.
- Errores: clases de `src/errors.ts`, nunca throw de strings.
- Nombrar tests `describe("nombreFuncion")` + `it("debe hacer X cuando Y")`.
- Ejemplo de service:
  ```ts
  export async function getProduct(id: string): Promise<Product> {
    const product = await db.product.findUnique({ where: { id } });
    if (!product) throw new NotFoundError(`Product ${id}`);
    return product;
  }
  ```

## NO hacer
- No anadir dependencias sin preguntar antes.
- No tocar `src/db/migrations/` a mano (usar `prisma migrate dev`).
- No usar `any`. strict mode esta activo por un motivo.
- No anadir `console.log`; usar el logger de `src/logger.ts`.
```

### Mantenerlo vivo
Un `AGENTS.md` obsoleto es PEOR que ninguno: el agente seguira instrucciones
que ya no aplican. Revisa y actualiza cuando cambie el stack.

---

## 2. Contexto progresivo

No uses todo el repo como contexto. Escalona:

| Momento | Que le das |
|---|---|
| Sesion nueva | Solo `AGENTS.md` (ya se carga solo) |
| Feature pequena | El archivo objetivo + 1-2 relacionados |
| Feature mediana | El arbol (`src/`) + AGENTS.md |
| Debug | El archivo + el error exacto |
| Exploracion | "Mapea la arquitectura" como tarea separada |

### Tecnica: el agente como navegador de codigo
Antes de una feature grande:
> "Mapea este repo: cual es el flujo de una peticion HTTP desde que entra hasta
> que llega a la DB. Dame el diagrama en texto, con rutas de archivo. No
> escribas codigo."

Cuesta 2 minutos y evita que el agente invente una arquitectura paralela a la
tuya.

---

## 3. Entorno de desarrollo y git

### Por que git es obligatorio
Sin git, un agente puede pisar 20 cambios buenos con uno malo y no hay vuelta
atras. Con git, `git diff` es tu red de seguridad Y tu herramienta de revision
del filtro 1 (alcance).

Instala git (ver `07-entorno-windows.md`). Minimos:
```bash
git init
git add .
git commit -m "chore: setup inicial"
```
Regla personal: **commit atomico por tarea del agente**. Si el agente hizo 3
cosas, revisa y separa en 3 commits. Asi puedes revertir una sin las otras.

### El poder de "commit por paso"
1. "Implementa el paso 1."  -> verificar -> `git commit`
2. "Implementa el paso 2."  -> verificar -> `git commit`
3. "Implementa el paso 3."  -> verificar -> `git commit`

Si el paso 3 sale mal, `git revert` del ultimo commit y el 1 y 2 siguen ahi. Sin
esto, un fallo te obliga a deshacer a mano cambios buenos.

---

## 4. Tests con agente

El agente escribe tests rapido, pero tests malos si no lo guias.

### El test debe fallar antes de existir (regla de oro del testing con IA)
> "Escribe un test para [funcion] que falle con la implementacion actual."

Si el test pasa contra codigo roto, no sirve. Y obligar al test a fallar revela
si el agente entiende la funcion.

### Guia al agente
> "Para cada funcion nueva escribe tests. Cada test: Arrange, Act, Assert
> explicito. Cubre el camino feliz y al menos un caso limite o de error. Nombre
> del test: `debe [resultado] cuando [condicion]`. Usa el estilo de test de
> `tests/ejemplo.test.ts`."

### Correr los tests SIEMPRE
> "Ejecuta `npm run test` y pega la salida real. Si un test falla, no digas que
> terminaste."

---

## 5. Seguridad minima del entorno (ANTES de perder archivos)

Hazlo ANTES del nivel 4:
- El agente trabaja solo dentro de la carpeta del proyecto.
- Comandos destructivos bloqueados: `rm -rf`, `git push --force`, `git reset
  --hard` sin confirmacion.
- Revisa diffs antes de hacer commit. `git diff` es tuantes de red.
- Nunca le des acceso a credenciales reales. Usa `.env.example` en el repo y
  `.env` local en `.gitignore`.

---

## 6. EjercicioPractico
Toma un proyecto tuyo (o el CLI del nivel 2) y:
1. Escribe un `AGENTS.md` completo con TODO lo de la seccion 1.
2. Pide al agente "mapea la arquitectura" y compara con la tuya.
3. Abre sesion nueva y pide una feature sin contexto extra.
4. Mide: cuantas correcciones necesitaste?

---

## Resumen
- `AGENTS.md` es la palanca #1. Invierte aqui primero.
- Contexto progresivo, no vuelques el repo.
- Git = red de seguridad + filtro de alcance. Commit atomico por paso.
- Tests que no han fallado nunca, no verifican nada.
- Seguridad minima ANTES de dar autonomia.

Siguiente: [Nivel 4 - Orquestacion](04-nivel-4-orquestacion.md)