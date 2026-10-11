# Guía Técnica: Desarrollo de Software con Agentes de IA

Ruta de aprendizaje desde lo esencial hasta la operación profesional de agentes
de código en un equipo real.

---

## Qué es esto

Un sistema de 6 niveles. Cada nivel tiene un objetivo, entregables concretos y
**criterios de dominio** (puedes pasar al siguiente cuando los cumples, no cuando
"lo leíste").

| Nivel | Nombre | Objetivo |
|---|---|---|
| 0 | [Plan de ruta](00-plan-de-ruta.md) | Mapa completo, entorno y orden |
| 1 | [Fundamentos técnicos](01-fundamentos.md) | Entender qué hay dentro del agente |
| 2 | [Colaboración básica](02-nivel-2-colaboracion.md) | Escribir código con criterio |
| 3 | [Contexto y reglas](03-nivel-3-contexto-y-reglas.md) | Que el agente trabaje en *tu* repo |
| 4 | [Orquestación](04-nivel-4-orquestacion.md) | Multi-agente, MCP, skills |
| 5 | [Producción](05-nivel-5-produccion.md) | CI, review, seguridad, coste |
| 6 | [Escala profesional](06-nivel-6-escala.md) | Sistemas de agentes en producción |

Extras: [entorno Windows](07-entorno-windows.md) · [plantillas](plantillas/)

### Tu ruta especifica
> [Plan C# / ASP.NET Core -> Web moderna (8 semanas)](08-plan-csharp-web.md)
> Si vienes de ASP.NET Core MVC, SQL Server y HTML, empieza por ahi.

---

## Tu perfil (verificado)

| Lo que tienes | Estado |
|---|---|
| ASP.NET Core MVC | Soportado y first-party en .NET 10. No obsoleto. |
| C# | Vigente. .NET 10.0 LTS trae C# 14. |
| SQL Server | Vigente. De lo mas valioso que tienes. |
| HTML | Vigente; te falta el nivel de componentes. |

Stack recomendado: **.NET 10 LTS** (SDK 10.0.401) + **EF Core 10** +
**Blazor Web Apps** + **SQL Server**. Complemento recomendado: **TypeScript**.

---

## Regla de oro (léela antes que todo)

> **Un agente no es un programador. Es un programador competente con amnesia y
> acceso a un desbordamiento de oportunidades.**

Tres hechos que explican el 90% de los fracasos con agentes:

1. **El agente no recuerda.** Cada sesión empieza en cero. Lo que no está escrito
   en un archivo, no existe.
2. **El agente no ejecutó nada hasta que tú se lo dices.** Si no corres tests, no
   sabes si el código funciona. Ni él.
3. **El agente dice "sí" a todo.** Escribe código plausible, no código correcto.
   La diferencia la pone la verificación, no la confianza.

Todo lo demás en esta guía es engineering alrededor de esos tres hechos.

---

## Cómo usar esta guía

1. Lee `00-plan-de-ruta.md` completo.
2. Prepara tu entorno con `07-entorno-windows.md`.
3. Avanza nivel a nivel. Marca el checklist de dominio de cada nivel.
4. Copia `plantillas/AGENTS.md` a tu primer repo en cuanto llegues al nivel 3.
5. Vuelve a los niveles anteriores cuando algo falle. Es normal.

---

## Glosario mínimo

| Término | Significado |
|---|---|
| **LLM** | Modelo de lenguaje. Predice texto. No sabe si lo que dice es cierto. |
| **Agente** | LLM + herramientas + bucle de acción/observación. |
| **Contexto** | La "memoria" del agente. Escara y se llena. |
| **Tool / Herramienta** | Función que el modelo puede invocar (leer archivo, correr comando). |
| **AGENTS.md** | Archivo de instrucciones del repo, leído por el agente al arrancar. |
| **MCP** | Protocolo estándar para conectar fuentes de datos externas al agente. |
| **Skill** | Paquete de instrucciones reutilizables para una tarea concreta. |
| **Hook** | Automatización determinista que se dispara en eventos. |
| **Compaction** | Cuando el contexto se llena, el agente resume y pierde detalle. |