# Nivel 2 - Colaboracion basica

## Objetivo
Cambiar el patron mental de "pedir codigo" a "dirigir ingenieria".

---

## 1. El bucle fundamental: Planear -> Implementar -> Verificar

Este es EL patron. Todo lo demas son variaciones.

### Fase 1: Planear (antes de que escriba una linea)
El error mas caro es pedir codigo directamente. Primero:

> "Voy a anadir autenticacion JWT al servidor Express. Antes de escribir codigo,
> lee la estructura del proyecto y propongeme un plan en 4-6 pasos numerados.
> Indicame que archivos tocaria y que decisiones tecnicas tomarias. NO escribas
> codigo todavia."

**Que consigues:**
- Ves el razonamiento antes del resultado.
- Detectas misunderstandings cuando son baratos (5 palabras, no 200 lineas).
- El agente se compromete con un plan, que luego tiende a seguir.

**Que haces tu:** corriges el plan ANTES de que exista el codigo. Esto solo.

### Fase 2: Implementar (task minima)
Un paso del plan a la vez. Nunca los 6.

> "Implementa el paso 3 unicamente. En el archivo `src/middleware/auth.ts`."

### Fase 3: Verificar (inegociable)
> "Ejecuta `npm run typecheck` y `npm test`. Pega la salida real, completa. Si
> falla, no digas que funciona."

### Volver a Fase 1 con lo aprendido
El plan original es una hipótesis. Tras 2 pasos ya sabes más.

---

## 2. Tamano de la tarea

| Peticion | Resultado |
|---|---|
| "Construyeme una API completa" | Chatarra no verificable |
| "Anade el endpoint POST /users con validacion zod" | Perfecto |
| "Refactoriza este modulo" (sin criterio) | Diff enorme e ilegible |

**Regla:** una instruccion = un archivo (ideal) o una funcion. Si necesita un
"y", probably son dos instrucciones.

### El trick de los 3 pasos
Cuando la tarea es grande, pide primero:
> "Dame los 3 primeros pasos, en orden, sin hacerlos todavia."

Si los 3 pasos son razonables, dale luz verde. Si el paso 1 ya es vago, corrige
antes de gastar contexto. Este patron recorta drásticamente el trabajo
desperdiciado.

---

## 3. Como revisar codigo generado por IA

Diff de 200 lineas no se revisa. Revisa en 4 filtros, en este orden:

Revisa en estos 4 filtros, en este orden:

### Filtro 1 - Alcance
¿Modifico archivos que no debia? `git diff --stat` te da el mapa en 1 segundo.
Ese es el filtro mas barato y el que mas se falla.

### Filtro 2 - Complejidad innecesaria
El agente prefiere el caso general al caso concreto. Busca:
- Abstracciones para un solo uso (interfaces, factories, wrappers).
- Manejo de errores para casos que no pueden ocurrir.
- Configurabilidad de cosas que nunca vas a configurar.

Criterio: "esto seria mas simple con 15 lineas en vez de 60?" -> Si, simplifica.

### Filtro 3 - Alucinacion
Busca llamadas a funciones/metodos y confirma que existen:
- Imports que no son de dependencias instaladas.
- Metodos de librerias que no existen en la version que tienes.
- Nombres de tests que no existen.

El compilador/typechecker atrapa casi todo. Si pasa, es porque tu no tienes
typechecking (ve al nivel 3).

### Filtro 4 - El test
El test es la prueba de que no hubo simulacion de exito. Pregunta siempre:
- "Que caso cubre este test?"
- "El test falla si rompo la funcion a proposito?" (test de mutacion mental)

Un test que pasa siempre no verifica nada.

---

## 4. Iteracion: el motor real

La mayoria del valor esta en la segunda, tercera y cuarta iteracion, no en el
prompt inicial. Flujo:

```
Intento 1  ->  lees error  ->  corriges prompt  ->  Intento 2
                                                          |
Intento 4  <-  corriges prompt  <-  lees error  <-  Intento 3
```

Truco para diagnosticar: cuando falla, no repitas el prompt. Cambia el *enfoque*:

| Sintoma | Cambio de enfoque |
|---|---|
| No entiende el requisito | Reformula con ejemplo concreto |
| Alucina APIs | "Lee los tipos reales en node_modules antes de usar" |
| Se repite en bucle | "Para. Diagnostica sin cambiar codigo" |
| Exceso de cambios | "Alcance: solo este archivo" |
| Dice que pasa sin ejecutar | "Pega la salida real del comando" |

---

## 5. Cuando no usar un agente
La honestidad aqui te ahorra horas. NO uses agente para:
- Decisiones de arquitectura (te va a dar la respuesta popular, no la correcta).
- Codigo critico de seguridad sin revision linea por linea.
- Cuando tu no entiendes el dominio (no podras revisar).
- Refactors mecanicos gigantes que puedes hacer con un script o formateador.

Usalo para: boilerplate, refactors repetitivos, tests, exploracion de codigo
ajeno, migraciones mecanicas, documentacion.

---

## 6. EjercicioPractico (obligatorio)
Construye un CLI de notas (Node o Python) solo con agente. Requisitos:

```
notas add "texto"      -> guarda con timestamp
notas list             -> lista
notas search "texto"   -> filtra
notas rm <id>          -> elimina
```

Metodo:
1. Plan completo primero (prompt de planificacion).
2. Feature por feature, verificando en cada una.
3. Tests para cada funcion.
4. En `notas/nivel2.md` anota: instruccion usada, intentos, error cometido, causa.

**Criterio de dominio:** terminas sin aceptar ni un cambio que no puedas
explicar tu.

---

## Plantillas utiles de este nivel

### Planificacion
> "[OBJETIVO]. Antes de codigo, lee [ARCHIVOS] y propon un plan en N pasos. Di
> que archivos tocas y que decisiones tomas. No escribas codigo."

### Ejecucion acotada
> "Ejecuta solo el paso [N]. Archivo: [RUTA]. Nada mas."

### Verificacion forzada
> "Ejecuta [COMANDO]. Pega la salida REAL. Si no lo ejecutaste, di 'no ejecutado'."

### Auto-critica antes de entregar
> "Antes de decir que terminaste: (1) ejecuta los tests, (2) lista que supuestos
> hiciste, (3) lista que casos NO cubriste."

### Anti-drift
> "Solo modifica [ARCHIVO]. Si otro archivo necesita cambios, describelos pero no
> los hagas."

---

## Resumen
- Planifica antes de codificar. Es la palanca mas rentable.
- Instruccion = un archivo.
- Verificar es tu trabajo; "pega la salida real" lo fuerza.
- Revisa en 4 filtros: alcance, complejidad, alucinacion, test.
- El valor esta en iterar, no en el prompt inicial.

Siguiente: [Nivel 3 - Contexto y reglas](03-nivel-3-contexto-y-reglas.md)