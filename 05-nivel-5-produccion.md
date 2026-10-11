# Nivel 5 - Produccion

## Objetivo
El agente pasa de ser una herramienta de escritorio a ser un componente de tu
pipeline, con las mismas exigencias que cualquier otro software.

---

## 1. Agentes en CI: donde empieza el valor real

Un agente en un pipeline de integracion continua trabaja con reglas claras
(los gates), sin sesgo de comodo, y produce artefactos revisables.

### Casos de uso de alto valor y bajo riesgo

| Caso | Riesgo | Valor |
|---|---|---|
| Revisar PRs buscando patrones | Bajo | Alto |
| Generar tests para codigo nuevo | Bajo | Alto |
| Triage de issues (clasificar,Duplicados) | Bajo | Medio |
| Resumir changelogs y releases | Bajo | Medio |
| Detectar codigo obsoleto (deps) | Bajo | Medio |
| Refactors mecanicos | Medio | Alto |
| Migraciones de DB | Alto | Alto |

Empieza por los de riesgo bajo. Gana confianza antes de delegar migraciones.

### Como integrarlo (patron comun)
1. El PR se abre.
2. El agente (en el runner de CI) recibe el diff.
3. Devuelve un informe estructurado (no prosa libre).
4. El informe se postea como comentario del PR y/o falla el check segun unos
   umbrales.
5. La decision final sigue siendo humana.

---

## 2. Quality gates: reglas deterministas

La parte importante NO es el agente, es el gate. El gate es codigo que bloquea.

### Gates recomendados (de mas a menos severo)
1. **Typecheck / compilacion.** Determinista. Si falla, bloquea. No necesita agente.
2. **Tests.** Determinista. Bloquea si falla.
3. **Lint / formato.** Determinista. Bloquea.
4. **Umbral de cobertura.** Bloquea si baja del minimo.
5. **Revision de agente.** Advertencia o bloque segun severidad y confianza.

**Principio:** cuanto mas grave el fallo, mas determinista debe ser el gate. Un
"el agente creo que estaba bien" no es un gate.

### Anti-patron: el agente como unico gate
Si tu unica proteccion es "un agente revisa", has puesto a un componente no
determinista como puerta unica de calidad. Usa al agente como capa ADICIONAL, con
los gates deterministas debajo.

---

## 3. Revision de seguridad para codigo generado por IA

El codigo de IA tiene sesgos de seguridad predecibles. Revisa especificamente:

| Riesgo | Senal tipica |
|---|---|
| Inyeccion SQL | Concatenacion de strings en queries |
| Inyeccion de comandos (shell) | `exec`/`execSync` con input del usuario |
| Secretos hardcodeados | API keys, tokens, contrasenas en el codigo |
| Validacion de entrada ausente | Sin sanitizacion en bordes |
| Auth/authz incorrecta | Falta comprobacion de permisos en un endpoint |
| Dependencias maliciosas | Paquete sospechoso con muchos downloads |
| XSS | Renderizado de HTML sin escapar |
| Datos sensibles en logs | Imprimir tokens/contrasenas |

### El sesgo de "parece seguro"
La IA escribe codigo que PARECE production-ready: tiene validaciones
superficiales, nombres correctos, estructura limpia. Pero pasa por alto
detalles de seguridad. El filtro humano en esto es obligatorio.

---

## 4. Politica del equipo sobre codigo generado por IA

Ten esto por escrito (en `AGENTS.md` o doc del equipo):
- Que partes del repo PROHIBIDO tocar con IA (pago, auth, migraciones).
- Que se requiere revision humana mas estricta para que.
- Que los commits indiquen el origen (por ejemplo, "Co-Authored-By" o prefijo).
- Como se prueba el codigo generado antes de mergear.

---

## 5. Medicion: sin numeros no hay mejora

Sin metricas, "los agentes nos ahorran tiempo" es una creencia. Mide:

### Metricas clave
| Metrica | Como se mide | Bueno |
|---|---|---|
| Tasa de aceptacion de PRs de IA | PRs mergeados / PRs generados | > 60-70% |
| Tiempo de revision humana por PR | Minutos en revisar | Baja respecto a codigo a mano |
| Ratio gold-plating | PRs que habrias pedido / PRs generados | Cercano a 1 |
| Hallazgos en revision | Bugs/seguridad hallados por revision | Mas = proceso serio |
| Coste por tarea | Tokens gastados / feature | Decreciente con contexto |
| Regresiones post-merge | Bugs de codigo IA en prod | Bajo |

### La metrica mas honesta: tiempo de ciclo real
De "idea a produccion", con y sin agentes. Si no mejora, el proceso no esta
maduro todavia.

---

## 6. Cuando un agente se equivoca en produccion
- **Rollback rapido**: el gate determinista (tests) es tu red. Escala rapido.
- **Trazabilidad**: que el codigo generado por IA este marcado para poder
  identificar la fuente de un bug.
- **Postmortem sin culpa**: cuando falle, la pregunta es "¿que gate/regla faltó?",
  no "¿quien lo escribio?". Corrige el proceso.

---

## Resumen
- Agentes en CI, empezando por casos de riesgo bajo (review, tests, triage).
- Los gates deterministas van DEBAJO del agente. El agente no es el unico porton.
- Revisar seguridad de codigo IA con checklist especifico.
- Politica por escrito en el equipo.
- Medir: aceptacion, tiempo de revision, coste, regresiones.

Siguiente: [Nivel 6 - Escala profesional](06-nivel-6-escala.md)