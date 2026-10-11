# Plan de ruta: de cero a equipos con agentes

## Mapa visual

```
Nivel 1  Fundamentos           -->  Entiendes la máquina
   |
Nivel 2  Colaboración basica    -->  Produces codigo correcto a pequena escala
   |
Nivel 3  Contexto y reglas      -->  El agente entiende TU proyecto
   |
Nivel 4  Orquestacion           -->  Multiples agentes, herramientas propias
   |
Nivel 5  Produccion             -->  El agente entra en tu CI con Quality Gates
   |
Nivel 6  Escala profesional      -->  Sistemas de agentes medidos y rentables
```

No saltes niveles. El error mas caro es llegar al 4 sin tener el 3: multiplicas
agentes que no entienden tu repo y el resultado es caos a 3x velocidad.

---

## NIVEL 1 — Fundamentos tecnicos
**Tiempo estimado:** 3-5 dias

### Objetivo
Dejar de ver el agente como "un chat que programa" y entenderlo como un sistema
con piezas, limites y modos de fallo predecibles.

### Que leer
-> `01-fundamentos.md` completo.

### Entregables
- [ ] Explicar sin mirar apuntes la diferencia entre LLM, chat y agente.
- [ ] Describir el bucle agente -> accion -> observacion -> accion.
- [ ] Saber que es la ventana de contexto y por que se llena.
- [ ] Enumerar 5 modos de fallo del agente y su causa raiz.

### Criterio de dominio
> Le pides a un agente que implemente una funcion de 30 lineas en un archivo que
> no conoce. El lee el archivo, la implementa bien a la primera y **tu no tuviste
> que corregir nada estructural**. Si tuviste que decirle que metodos existian o
> como se llamaba el archivo, no dominas el nivel 1.

---

## NIVEL 2 — Colaboracion basica
**Tiempo estimado:** 1-2 semanas

### Objetivo
Cambiar el patron mental de "pedir codigo" a "dirigir ingenieria". Dejar de
aceptar codigo sin entender y empezar a usar el agente como aumento de cobertura.

### Que leer
-> `02-nivel-2-colaboracion.md`

### Entregables
- [ ] Usar el bucle **Planear -> Implementar -> Verificar** de forma sistematica.
- [ ] Mantener tasks de 1 archivo por iteracion.
- [ ] Nunca aceptar un cambio sin ver la salida del typecheck/lint/tests.
- [ ] Saber leer un diff de IA y detectar alucinacion de API en menos de 1 minuto.

### Practica obligatoria
Construye un CLI pequeno (Node o Python) de principio a fin **solo con agente**,
y documenta en `notas/nivel2.md`:
- Que instrucciones funcionaron y cuales no.
- Cuantos intentos necesito cada feature.
- Que errores cometio y por que.

### Criterio de dominio
> Terminas un proyecto completo sin aceptar ni un cambio que no puedas explicar
> tu. Si hay codigo en tu repo que no entiendes, no has pasado el nivel.

---

## NIVEL 3 — Contexto y reglas
**Tiempo estimado:** 2-3 semanas

### Objetivo
Que el agente funcione en **tu** repositorio sin que le expliques la estructura
cada vez. Aqui es donde se gana o se pierde la productividad.

### Que leer
-> `03-nivel-3-contexto-y-reglas.md`

### Entregables
- [ ] `AGENTS.md` propio, completo y mantenido.
- [ ] Repo con estructura predecible y convenciones visibles.
- [ ] Flujo git basico: rama, commit atomico, PR.
- [ ] Tests unitarios escritos por el agente que pasan de verdad.
- [ ] Tecnica de "contexto progresivo": ensenar lo justo en cada paso.

### Criterio de dominio
> Abres el repo en una sesion nueva, pides una feature de 3 archivos sin dar
> contexto previo, y el agente la implementa respetando tus convenciones,
> con tests, y **sin inventarse APIs**.

---

## NIVEL 4 — Orquestacion
**Tiempo estimado:** 3-4 semanas

### Objetivo
Dejar de hablar con un agente y empezar a disenar un sistema de agentes.

### Que leer
-> `04-nivel-4-orquestacion.md`

### Entregables
- [ ] Subagentes con contextos aislados y especializados.
- [ ] Al menos un servidor MCP propio (filesystem, base de datos, API interna).
- [ ] Skills reutilizables para tus tareas mas frecuentes.
- [ ] Hooks para forzar validaciones en puntos concretos.
- [ ] Paralelizacion de trabajo independiente.

### Criterio de dominio
> Delegas "revisa este PR en busca de X" a un subagente y te devuelve un informe
> accionable sin contaminar tu contexto. Si al volver necesitas "resumeme que
> paso", el diseno esta mal: el subagente debe devolver **solo la conclusion**.

---

## NIVEL 5 — Produccion
**Tiempo estimado:** 4-6 semanas

### Objetivo
El agente deja de ser una herramienta de escritorio y pasa a ser un componente
de tu pipeline con las mismas exigencias que cualquier otro.

### Que leer
-> `05-nivel-5-produccion.md`

### Entregables
- [ ] Agente en CI: review automatico de PRs, generacion de tests, triage.
- [ ] Quality gates que **bloquean** el merge si algo falla.
- [ ] Revision de seguridad con checklist especifico para codigo generado por IA.
- [ ] Medicion: coste por tarea, tasa de aceptacion, tiempo de ciclo.
- [ ] Politica del equipo sobre codigo generado por IA.

### Criterio de dominio
> Un PR generado integramente por agentes pasa tus gates de CI, es revisado por
> un humano en menos tiempo del que llevaria revisar codigo escrito a mano, y
> puedes demostrar con numeros que el ciclo se ha acelerado.

---

## NIVEL 6 — Escala profesional
**Tiempo estimado:** Continuo

### Objetivo
Operar agentes como infraestructura: con SLOs, presupuesto, evaluacion
automatica y plan de contingencia.

### Que leer
-> `06-nivel-6-escala.md`

### Entregables
- [ ] Evaluacion automatica (dataset de tareas -> score -> regresion detectada).
- [ ] Control de coste por equipo/proyecto con alertas.
- [ ] Estrategia multi-modelo (barato para lo mecanico, caro para lo dificil).
- [ ] Fallback manual y criterio de "cuando apagar los agentes".
- [ ] Pipeline de mejora: los fallos reales alimentan el dataset de evaluacion.

### Criterio de dominio
> Puedes responder con datos: "este agente cuesta X al mes, ahorra Y horas,
> y falla en Z% de los casos de forma conocida y monitorizada".

---

## Reglas transversales (aplican a todos los niveles)

### 1. El agente es unBecariosr muy rapido con errores de novato
Tratalo como eso: hace mucho, rapido, y con errores tipicos de quien esta
aprendiendo. Pidele siempre los 3 primeros pasos. Que no se adelante.

### 2. Contexto > prompt
Invertir tiempo en un buen `AGENTS.md` rinde mas que cualquier truco de prompt.
El prompt es tactico; el contexto es estrategico.

### 3. La verificacion es tuya
El agente **no sabe** si su codigo compila hasta que lo ejecutas.
Automatiza esa ejecucion para no depender de tu atencion.

### 4. El bucle de 2 pasos gana a la idea brillante
Intenta -> lee el error -> corrige. La mayor parte del valor esta en esto, no en
el prompt inicial.

### 5. Nunca dejes al agente sin limites
Fuera del directorio del proyecto, red restringida, comandos destructivos
bloqueados. Configura esto ANTES del nivel 3.

---

## Errores que veras en ti mismo (marcarlos es parte del aprendizaje)

- [ ] Aceptar codigo sin leer -> vuelve a "No commit sin entender".
- [ ] Prompt gigante de 200 lineas -> vuelve a nivel 2, tarea minima.
- [ ] `AGENTS.md` que es un copia del README -> nivel 3.
- ] Delegar el agente cuando no sabes que hace -> nivel 1.
- [ ] Meter agentes en produccion sin gates -> nivel 5 pendiente.

---

## Seguimiento

Lleva tu propio registro. Crea `notas/progreso.md` con una linea por sesion:

```markdown
| Fecha | Nivel | Tarea | Resultado | Que aprendi |
|-------|-------|-------|-----------|-------------|
| 2026-10-11 | 1 | Agente lee repo y crea funcion | OK, 0 correcciones | Necesitaba indicar archivo |
```

Esa tabla vale mas que cualquier curso, porque es tu mapa de referencia real.