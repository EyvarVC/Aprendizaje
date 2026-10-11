# Nivel 6 - Escala profesional

## Objetivo
Operar agentes como infraestructura: con SLOs, presupuesto, evaluacion
automatica y plan de contingencia.

---

## 1. Evaluacion automatica (lo que separa un toy de un sistema)

Un sistema de agentes en produccion necesita responder: "mejoro o empeoro?"

### Dataset de evaluacion
Un conjunto de tareas reales (con su resultado esperado) que se ejecuta
periodicamente contra el agente.

Ejemplo de dataset:
```json
[
  {
    "task": "Anade validacion de email al formulario de registro",
    "archivos_esperados": ["src/forms/Registro.ts"],
    "tests_deben_pasar": ["validacion.email.test.ts"],
    "criterios": ["no rompe otros forms", "respeta zod del proyecto"]
  }
]
```

### Como se evalua
1. El agente ejecuta cada tarea del dataset.
2. Se corre la suite de tests de tu repo (determinista).
3. Se mide si los criterios se cumplen (puede ser un LLM-as-judge para lo
   subjetivo, o checks deterministas para lo objetivo).
4. Se compara con la ejecucion anterior.

### Lo que detectas
- Regresiones tras cambiar el prompt o el modelo.
- Cuando una actualizacion de dependencia rompe al agente.

---

## 2. Control de coste

El gasto de agentes escala con el uso. Sin control, la sorpresa arrives en la
factura.

### Palancas
- **Contexto vs. prompt.** Reutilizar `AGENTS.md` bien hecho es mas barato que
  explicarte cada vez (no pagas mil veces lo mismo si lo lees del archivo).
- **Modelo adecuado.** Modelo caro para razonamiento dificil; modelo barato
  (rapido) para tareas mecanicas (formatear, renombrar, tests boilerplate).
- **Subagentes para tareas de un solo uso.** No mantengas un agente grande para
  una tarea trivial.
- **Cache.** Si tu tooling lo soporta, cachea resultados de tareas repetidas.

### Alertas
Pon presupuesto por equipo/proyecto. Cuando el gasto se dispara sin que suba
el valor, algo va mal (un agente en bucle, un prompt que genera demasiado).

---

## 3. Estrategia multi-modelo

| Tipo de tarea | Modelo sugerido | Razon |
|---|---|---|
| Refactor mecanico, formateo | Rapido/barato | deterministicidad, volumen |
| Tests boilerplate | Rapido/barato | patron repetitivo |
| Buscar bug en log | Rapido/barato | buscar y resumir |
| Disenar feature | Capaz | necesita razonamiento |
| Refactor de arquitectura | Muy capaz | juicio y trade-offs |
| Revision de seguridad | Muy capaz | los falsos negativos importan |

El error tipico: usar el modelo mas caro para todo. Se paga 10x por tareas que
un modelo barato hace igual de bien.

---

## 4. Operacion: SLOs y fallback

### SLOs simples para un agente
- Disponibilidad: el agente esta disponible X% del tiempo.
- Latencia: una tarea tipica tarda menos de Y.
- Calidad: las salidas pasan los gates un Z% de las veces.
- Coste: coste por tarea dentro de presupuesto.

### Fallback manual
Define de antemano: "si el agente falla 3 veces seguidas en una tarea, lo hago yo
y lo anoto". Ten un interruptor manual. Un sistema que no puedes apagar es un
sistema que no controlas.

---

## 5. Pipeline de mejora continua

El nivel 6 se sostiene con este ciclo:

```
  Fallo real en produccion
          |
          v
  Anadir caso al dataset de evaluacion
          |
          v
  Medir: el agente falla ese caso?
          |
     SI                            NO
      |                            |
  Ajustar prompt/        (busca otro fallo)
  AGENTS.md/skill                    |
      |                              |
      v                              |
  Re-ejecutar dataset  <-------------
          |
          v
  Publicar version mejorada
```

Cada fallo real se convierte en un test permanente. Asi el sistema no repite
errores y mejora con evidencia, no con intuicion.

---

## 6. Lo que sigue despues del nivel 6
- Orquestacion multi-agente avanzada (agentes que coordinan agentes).
- Agentes persistentes con memoria entre sesiones (no solo archivos).
- Evaluacion con LLM-as-judge afinada a tu dominio.
- Integracion con datos de produccion para mejora continua.

---

## Resumen del nivel
- Dataset de evaluacion para detectar regresiones.
- Control de coste con alertas y palancas.
- Multi-modelo: caro para lo dificil, barato para lo mecanico.
- SLOs y fallback manual.
- Ciclo: fallo -> dataset -> ajuste -> re-evaluacion -> mejora.

---

## Fin de la guia
Vuelve a `README.md`. Tu siguiente paso: elige un proyecto real, aplica los
niveles en orden, y lleva tu registro en `notas/progreso.md`.