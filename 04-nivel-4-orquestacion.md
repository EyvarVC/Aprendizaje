# Nivel 4 - Orquestacion

## Objetivo
Dejar de hablar con un agente y empezar a disenar un sistema de agentes.

---

## 1. Subagentes: aislamiento de contexto

### Que son
Un agente que invoca a otro agente con su propio contexto separado. El
principal le delega una tarea, el subagente la hace en silencio, y devuelve solo
la conclusion.

### Por que aislar contexto
El nivel 1 te dijo: el contexto es el recurso escaso. Un subagente te protege de
que 200 lineas de busqueda te llenen la ventana. Es el patron mas importante
del nivel.

### Patron de uso correcto
```
Orden principal (tu sesion, contexto limpio):
  "Revisa el PR #42 y dime si hay problemas de seguridad."
        |
        v
  Subagente (contexto aislado):
    - lee el diff (200 lineas aqui, no en tu contexto)
    - busca SQL injection, secretos, etc.
    - escribe su informe
        |
        v
  Conclusion (vuelve al principal, SOLO esto):
    "2 hallazgos: (1) SQL injection en line 40, (2) API key hardcoded en
     config.js. Ambos criticos."
```

Tu contexto recibio 3 lineas, no 200. Eso es todo el punto.

### Cuando delegar
- La tarea requiere leer mucho pero la respuesta es corta. (revisar un PR, buscar
  un bug, resumir un modulo)
- La tarea es independiente y puede ir en paralelo.
-Quieres un "especialista" con enfasis diferente (seguridad, performance,
  estilo).

### Cuando NO delegar
- La tarea necesita el hilo de la conversacion actual.
- La respuesta es larga y la vas a necesitar en detalle.
- Es la tarea principal en la que estas trabajando.

### Fallo tipico de diseño
> [!CAUTION]
> Si al volver necesitas "resumeme que paso", el subagente esta devolviendo
> demasiado. Debe devolver SOLO la conclusion accionable. Fijate el criterio de
> dominio del plan.

---

## 2. Herramientas propias (allowlist / custom tools)

Cuanto mas controlas que puede invocar el agente, mas seguro y mas preciso.

### Principio de minimo privilegio
El agente deberia poder hacer SOLO lo que necesita para esta tarea. Un agente
que puede ejecutar cualquier comando puede borrar tu base de datos.

Para una tarea de solo lectura (revisar codigo), dale solo lectura. Para una
tarea de edicion, dale editar pero no ejecutar comandos destructivos.

---

## 3. MCP: conectar datos y herramientas

### Que es
Model Context Protocol: un estandar para que el agente use fuentes de datos y
herramientas externas de forma uniforme (tu base de datos, tu API, tu Jira, tu
documentacion, tu navegador...).

### Cuando aporta mucho
- Consultar tu esquema de base de datos real (evita alucinacion de columnas).
- Leer documentacion interna / runbooks.
- Dar al agente acceso a datos de negocio para generar features correctas.

### Ejemplo de uso (conceptual)
Un servidor MCP de "schema de la DB" que, cuando el agente escribe una query,
le da el esquema real. Resultado: deja de inventarse nombres de tablas y
columnas, que es el fallo mas comun en tareas de datos.

### Cuando NO usar MCP
Para una tarea puntual, un script o un `AGENTS.md` con el esquema basta. MCP es
para cuando la fuente de datos cambia o la conectas a menudo. No sobreconstruyas.

---

## 4. Skills: tareas repetibles

### Que es
Un paquete de instrucciones reutilizable para una tarea concreta. Piensalo como
un "macro": en vez de reescribir 20 lineas de instrucciones cada vez, lo
llamas por nombre.

### Como se define (concepto general)
Una carpeta/skill con un archivo de instrucciones. Cuando invocas la skill, el
agente carga esas instrucciones como si las hubieras dado.

### Ejemplo: skill "nueva-feature"
Instrucciones estandar para crear una feature en TU repo:
- Crea test primero.
- Sigue la estructura de `src/services/`.
- Ejecuta `npm run test` y pega la salida.
- Actualiza `docs/` si aplica.
- Commit atomico.

### Cuando crearlas
Cuando haces la misma secuencia de pasos 3+ veces, es candidata a skill. No antes.

---

## 5. Hooks: automatizar la verificacion

### Que es
Un script que se ejecuta automaticamente en un evento (antes de editar, antes de
commit, al terminar una tarea). A diferencia del prompt, un hook NO depende de
que el agente "se acuerde": se dispara siempre.

### El hook mas valuable: "no commit sin verificar"
Si tu tooling lo soporta, un hook pre-commit que corra `npm run typecheck` y
`npm test` y BLOQUEE el commit si fallan. Esto mata la "simulacion de exito" de
golpe, sin depender de que tu lo recuerdes.

### Diseno
Un hook debe ser determinista: "si X, entonces Y". No le pidas al LLM que
"sea severo". La severidad la pones en codigo.

---

## 6. Paralelizar trabajo independiente

Si tienes 3 tareas sin dependencias mutuas (3 modulos, 3 reviews, 3 tests),
puedes lanzarlas en paralelo con subagentes. El tiempo total pasa de
suma a maximo.

Cuidado:
- Solo paraleliza si no comparten archivos (si comparten, hay race condition).
- Cada subagente debe devolver solo su conclusion.
- Tu integras los resultados al final.

---

## 7. Anti-patrones del nivel

| Anti-patron | Por que falla | Que hacer |
|---|---|---|
| Delegar sin criterio de retorno | Subagente devuelve un muro de texto | Exigir conclusion accionable |
| MCP para todo | Sobreconstruccion | Empieza con AGENTS.md |
| Skill antes de tener repeticion | Mantenimiento sin valor | Espera 3 repeticiones |
| 5 subagentes sin integrar |trabajo perdido | Un punto de integracion claro |
| Confiar en hook de LLM para seguridad | No es determinista | Codigo para seguridad |

---

## 8. EjercicioPractico
1. Crea un subagente de "revision de seguridad" que, dado un diff, devuelva solo
   hallazgos criticos.
2. Define un skill "nueva-feature" y usalo 2 veces.
3. Configura un hook que fuerce typecheck antes de commit.
4. Con un cambio que no requiere Git todavia: instalalo (07-entorno-windows.md).

**Criterio de dominio:** delegas "revisa este PR" y te vuelve 3 lineas
accionables, sin contaminar tu contexto.

Siguiente: [Nivel 5 - Produccion](05-nivel-5-produccion.md)