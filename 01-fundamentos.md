# Nivel 1 - Fundamentos tecnicos

## 1. Las tres cosas que no son lo mismo

### LLM (modelo de lenguaje)
Una funcion `texto -> texto`. Recibe tokens, devuelve tokens. Es un predictor
estadistico entrenado con enormes cantidades de texto.

Lo que **no** tiene:
- No sabe si lo que dice es cierto. Suena seguro y se equivoca con la misma
  seguridad.
- No tiene memoria entre llamadas. Dos llamadas identicas dan respuestas distintas.
- No sabe que dia es hoy, ni que hay en tu disco, ni que version de una libreria
  usas.

### Chat con un LLM
LLM + historial de la conversacion. Sigue el hilo, pero sigue **dentro de la
conversacion**. Si le dices "hazlo", "eso" significa lo ultimo que dijo, y nada
mas. No puede mirar tus archivos.

### Agente
LLM + **herramientas** + **bucle de decision**. Esto es lo que anade el poder real:

```
   +-----------------------------------------+
   |  1. El modelo recibe contexto + objetivo |
   +--------------------+--------------------+
                        |
                        v
        El modelo decide: "necesito leer X"
                        |
                        v
   +-----------------------------------------+
   |  2. El sistema ejecuta la herramienta    |  <-- el mundo real
   +--------------------+--------------------+
                        |
                        v
        Observacion: contenido de X
                        |
                        v
   +-----------------------------------------+
   |  3. Vuelve al modelo con la observacion |
   +--------------------+--------------------+
                        |
                   Terminado?
                   /         \
                 SI          NO
                  |           |
            Respuesta     otro ciclo
```

La diferencia decisiva entre el chat y el agente: **el agente produce efectos
laterales reales**. Puede leer, escribir y ejecutar. Por eso puede Descriptive
correcto y tambien puede **destruir** tu proyecto.

---

## 2. La caja negra: la ventana de contexto

Todo lo que el agente "sabe" esta en una lista: instrucciones del sistema,
`AGENTS.md`, tu prompt, archivos que ha leido, salidas de comandos, y su propia
historia de tool calls. Esa lista es la **ventana de contexto**.

Propiedades que importan:

- **Finita.** Tipicamente de decenas a cientos de miles de tokens.
- **Cara en dinero.** Lo que entra se te cobra cada vez que se procesa.
- **Lenta de operar.** Contexto enorme = respuesta lenta.

### Contexto util vs. ruido

| Contexto | Util? |
|---|---|
| `AGENTS.md` con convenciones reales | SI, siempre |
| El archivo exacto que va a modificar | SI |
| Convenciones del proyecto | SI |
| Output de `npm test` con 800 lineas de warnings | Ruido |
| Historial de 50 turnos de correcciones | Ruido |
| Stack trace de un error que ya arreglaste | Ruido |

**Regla: alimenta el agente con el minimo contexto que le permita trabajar
correctamente.** Mas contexto no es mejor contexto.

### Compaction
Cuando el contexto se llena, algunos agentes lo "compactan": resumen lo
anterior y empiezan de cero. Consecuencia practica: **el agente pierde el
detalle fino de decisiones que tomo al principio**. Si una restriccion suya
("ojo, aqui no usamos `any`") se perdio en el resumen, la violara sin saberlo.

Por eso: **las reglas importantes van en `AGENTS.md`, no en el chat.** Lo que
esta en un archivo sobrevive a la compaction. Lo que escribiste en el chat, no.

---

## 3. Los 5 modos de fallo (y su causa raiz)

### 3.1 Alucinacion de API
> *"Uso `fs.promises.readFile` con la opcion `encoding: 'utf8'"* - cuando la
> funcion existe pero la opcion no. O inventa un metodo que no existe.

**Causa raiz:** el modelo predice el codigo mas *plausible* segun lo que vio
entrenado, no segun lo que hay instalado.

**Contramedida:** que el agente lea la definicion real de la API (en
`node_modules`, en el archivo de tipos, en la documentacion) antes de usarla.
Y que el compilador/typechecker separe entre tu y el error.

### 3.2 Deriva de requisitos
Pides "anade paginacion". En el turno 6 ya tambien ha refactorizado el modulo de
autenticacion "porque era mas limpio".

**Causa raiz:** el agente optimiza la coherencia global del codigo, y
"coherente" para el incluye cosas que tu no pediste.

**Contramedida:** alcance explicito y revision del diff.
> "Solo toca `src/api/list.ts`. No modifiques otros archivos. Si crees que otro
> archivo necesita cambios, dimelo pero no lo hagas."

### 3.3 Simulacion de exito
> *"Listo, he anadido los tests. Todos pasan."* - sin haberlos ejecutado.

**Causa raiz:** el agente escribe texto sobre lo que *deberia* ser verdad. No
distingue entre "esto funciona" y "esto parece que funciona".

**Contramedida:** obligar a que la verificacion sea visible.
> "Ejecuta `npm test` y pega la salida real. Si no lo has ejecutado, no digas que
> pasa."

Esta es la contramedida mas importante de toda la guia.

### 3.4 Contexto perdido / amnesia
Sesiones nuevas, el agente no recuerda la decision de arquitectura de ayer.

**Causa raiz:** diseno. La sesion no persiste el estado mental del agente.

**Contramedida:** persistir el conocimiento en archivos: `AGENTS.md`,
`docs/decisiones.md`, ADR (Architecture Decision Records).

### 3.5 Bucle infinito de intentar arreglar
El agente cambia A, rompe B, arregla B, rompe C... y en la iteracion 15 rompe
algo que funcionaba al principio.

**Causa raiz:** no tiene nocion de "frontera de progreso". Cada error lo trata
como el unico problema.

**Contramedida:** establecer un limite.
> "Llevas 5 intentos. Para y escribe que has intentado, que error persiste y que
> hipotesis tienes. No cambies mas codigo todavia."

---

## 4. Por que fallan los agentes: el sesgo de complacencia

Los modelos estan entrenados para ser utiles y agradables. Tienden a decir "si"
a lo que pides aunque la peticion sea mala idea.

Ejemplo real:
> Tu: "Anade retry a esta llamada sin timeout."
> Junior: "Claro! Aqui tienes el retry con backoff exponencial."
> Realidad: sin timeout, el retry nunca se agota.

El modelo optimiza **obediencia**, no correccion. Tu trabajo es monitorizar la
calidad, no solo la direccion. Pregunta siempre:
- "Que supongo aqui?"
- "Que pasa si esto falla?"
- "Hay un caso donde esto rompa?"

Y pide siempre que el agente te diga los supuestos que ha hecho.

---

## 5. Vocabulario que necesitas

| Termino | Significado real |
|---|---|
| **Token** | Unidad de texto (~4 caracteres). Unidad de medida y de precio. |
| **Tool call** | Invocacion de una herramienta. Tiene nombre y argumentos. |
| **Prompt** | La entrada de texto que le das. |
| **System prompt** | Instrucciones base del sistema. No las controlas del todo. |
| **Temperatura** | Aleatoriedad. Baja = determinista. Alta = creativo. |
| **RAG** | Recupera documentos relevantes y los mete en el contexto. |
| **Fine-tuning** | Reentrenar el modelo. Casi nunca es lo que quieres. |
| **AGENTS.md** | Instrucciones del repo leidas por el agente al arrancar. |
| **MCP** | Protocolo estandar para conectar herramientas/datos externos. |
| **Hook** | Script que se ejecuta en un evento (pre-commit, pre-edicion...). |
| **Subagente** | Agente hijo con su propio contexto, para tareas aisladas. |

---

## 6. Ejercicio de dominio

Antes de pasar al nivel 2, demuestra estos 4 puntos con el agente que uses:

1. **Prediccion de contexto.** Pidele "lee este archivo y dime que falta para que
   compile". Debe listar errores reales.
2. **No-te-lo-creo.** Pidele una feature y luego "ejecuta los tests y pega la
   salida". Debe ejecutar, no describir.
3. **Alucinacion deliberada.** Pidele usar una API y verifica que exista en los
   tipos instalados.
4. **Amnesia.** Cierra la sesion, abre otra, y confirma que no recuerda nada.

Si el punto 2 falla (te dice que pasa sin ejecutar), necesitas un hook o una
regla en `AGENTS.md` que lo fuerce. Eso es exactamente el material del nivel 3.

---

## Resumen del nivel

- LLM predice texto. Agente = LLM + herramientas + bucle.
- El contexto es el recurso escaso. Alimenta lo justo.
- Lo importante se escribe en archivos, no en el chat.
- Los fallos previsibles son: API inventada, deriva de alcance, exito simulado,
  amnesia, bucle infinito.
- El agente optimiza obediencia. Tu optimizas correccion.

Siguiente: [Nivel 2 - Colaboracion basica](02-nivel-2-colaboracion.md)