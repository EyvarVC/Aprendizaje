# Plan personalizado: C# / ASP.NET Core -> Web moderna (8 semanas)

> Este plan se integra con los 6 niveles de la guia de agentes. Practicas desarrollo
> web Y aprendes a dirigir agentes al mismo tiempo, con un solo proyecto.

## Tu punto de partida (revisado)

| Lo que tienes | Estado verificado (oct 2026) |
|---|---|
| ASP.NET Core MVC | Soportado y first-party en .NET 10. No obsoleto. Base solida. |
| C# | Vigente. .NET 10.0 LTS trae C# 14. |
| SQL Server | Vigente. Sigue siendo de lo mas valioso que tienes. |
| HTML | Vigente, pero te falta el nivel de componentes. |

**Correccion importante:** no necesitas migrar de nada. Tu framework es el correcto.
Lo que te falta es lo que hay AL REDEDOR de MVC.

## Tus huecos reales (ordenados por impacto)

| # | Hueco | Por que importa | Prioridad |
|---|---|---|---|
| 1 | **Frontend de componentes** (Blazor / React) | Razor views no escalan a apps grandes | Critico |
| 2 | **Tests automatizados** (xUnit) | Sin tests no puedes trabajar con agentes | Critico |
| 3 | **Git** | Red de seguridad + revision de diffs | Critico |
| 4 | **EF Core 10** | Si aun haces ADO.NET a mano, product. x10 | Alto |
| 5 | **TypeScript** | Donde los agentes de IA son mas fuertes | Alto |
| 6 | **Docker + despliegue** | Para poner cosas en produccion | Medio |
| 7 | **IA integrada en apps** | RAG, funciones de IA | Medio |

---

## Fase 0 - Entorno (dia 1-2)

Objetivo: todo instalado y verificado.

```powershell
# .NET 10 SDK
winget install --id Microsoft.DotNet.SDK.10

# Git (ya sabes que lo necesitas)
winget install --id Git.Git -e --source winget

# Docker Desktop (para SQL Server sin instalarlo nativo)
winget install --id Docker.DockerDesktop

git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
git config --global init.defaultBranch main
```

Editor: VS Code + extension **C# Dev Kit** (gratuita, de Microsoft).

Verificacion:
```powershell
dotnet --version          # debe decir 10.x
git --version
docker --version
```

**Criterio de dominio:** abres terminal y los tres comandos responden.

---

## Fase 1 - Fundamentos que faltan (semana 1)

Objetivo: Git y tests. Son la base de todo lo demas.

### Dia 1-2: Git
1. Crea un repo y haz los 5 comandos basicos: `init`, `status`, `add`, `commit`, `log`.
2. Practica ramas: `git switch -c feature/x`, merge, `git revert`.
3. Aprende `git diff` y `git diff --stat` (tu filtro de alcance para agentes).

### Dia 3-4: xUnit
1. Crea un proyecto de tests y ahi mete logica de negocio.
2. Aprende: `[Fact]`, `[Theory]`, `[InlineData]`, asserts, `Assert.Throws`.
3. Escribe 10 tests de una clase de servicio que ya tengas.

**Practica de agente (Nivel 2):**
> "Aqui esta `[mi-clase.cs]`. Escribe tests xUnit para todos los caminos: feliz,
> limites y errores. Nombre: `Deberia_[resultado]_Cuando_[condicion]`.
> Ejecuta `dotnet test` y pega la salida real."

**Criterio de dominio:** tienes 10 tests que pasan y sabes explicar cada uno.
Y usaste git todo el tiempo sin miedo.

---

## Fase 2 - Datos con EF Core 10 (semana 2)

Objetivo: EF Core + SQL Server real, con migraciones.

### Lo que aprendes
- `DbContext`, entidades, `DbSet<T>`
- Migraciones: `dotnet ef migrations add X` / `dotnet ef database update`
- LINQ a EF Core
- Consultas optimizadas (evita N+1 con `Include`)
- **`LeftJoin` / `RightJoin`** nativos: .NET 10 los anade a LINQ y EF Core los reconoce.
  Se acabo el `SelectMany` + `GroupBy` + `DefaultIfEmpty` para LEFT JOIN.

### Novedades de EF Core 10 (LTS hasta nov 2028) que te interesan
Verificadas en la documentacion oficial:

| Novedad | Por que te importa |
|---|---|
| **Busqueda vectorial nativa** | Tipo `vector` + `VECTOR_DISTANCE()` en SQL Server 2025. Con Linq: `OrderBy(b => EF.Functions.VectorDistance("cosine", b.Embedding, q))`. **Esto es la base del RAG** de la Fase 6. |
| **Tipo `json` nativo** | SQL Server 2025. EF mapea automatico si usas `ToJson()` en `ComplexProperty`. |
| **Complex types** | Son "value types" sin identidad. Evitan los problemas de los owned types y soportan `ExecuteUpdateAsync`. |
| **Named query filters** | Varios filtros por entidad con nombre, y puedes desactivar solo algunos: `IgnoreQueryFilters(["SoftDeletionFilter"])`. |
| **Seguridad mejorada** | Redacta constantes inline en los logs (no filtra datos sensibles) y añade un analizador que **avisa de concatenacion de strings en SQL crudo** (deteccion de SQL injection). |
| **`ExecuteUpdateAsync` con lambda normal** | Actualizaciones mas masivas sin montar expression trees a mano. |

> **Aviso de version:** la busqueda vectorial y el tipo `json` requieren
> **SQL Server 2025** o Azure SQL. Si usas una version anterior, quedan
> pendientes. El contenedor `mssql/server:2022-latest` de la Fase 2 no las tiene;
> para probarlas necesitas `mcr.microsoft.com/mssql/server:2025-latest`.

### Ejercicio
Levanta SQL Server en Docker:
```powershell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=TuPass123!" `
  -p 1433:1433 -d mssql/server:2022-latest
```
Conecta un proyecto MVC existente, crea 3 entidades, 3 migraciones.

**Practica de agente (Nivel 3):** escribe tu `AGENTS.md` con los comandos reales:
`dotnet build`, `dotnet test`, `dotnet ef migrations add`, `dotnet ef database update`.

**Criterio de dominio:** creas una entidad nueva con migracion y la ves en SQL
Server sin tocar una linea de SQL a mano.

---

## Fase 3 - Blazor Web Apps (semanas 3-4)

Objetivo: el salto a componentes sin salir de C#.

Por que Blazor primero y no React: te permite entender el modelo de componentes
sin aprender dos lenguajes a la vez. Cuando lo domines, React sera mas facil.

### Dia 1-2: Conceptos
- Componentes `.razor` vs vistas Razor (la diferencia clave)
- **Modos de renderizado** (esto es lo nuevo de .NET 10):
  - `StaticServer` (solo SSR, sin interactividad)
  - `InteractiveServer` (interactividad via SignalR)
  - `InteractiveAuto` / `InteractiveWebAssembly` (interactividad en cliente)
- Puedes **mezclarlos en la misma pagina**

### Dia 3-5: Lo nuevo de .NET 10 (verificado en la doc oficial)
- **Passkeys / WebAuthn** en Identity: autenticacion sin contrasena
- **`[PersistentState]`**: estado persistente declarativo entre prerender e interactividad
- **Validacion de formularios anidados** mejorada (con `[ValidatableType]`,
  usando source generators en vez de reflexion)
- **`NavigationManager.NotFound()`**: gestion de 404 nativa
- **`QuickGrid`** para tablas, con `RowClass`, filtros y columnas configurables
- **ResourcePreloader** para preload de assets
- **Metricas y tracing** del ciclo de vida de componentes
- **Persistencia de circuito**: sobrevive a pestanas en background o cortes de red

### Dia 6-10: Convertir tu CRUD de MVC a Blazor
Toma tu proyecto de la Fase 2 y rehaz la UI en componentes Blazor.
Compara: antes 3 archivos por pantalla (controller + view + model), ahora 1 componente.

**Practica de agente (Nivel 3-4):**
> "Convierte `[mi-controller]` a un componente Blazor. Usa el modo de renderizado
> `[InteractiveServer]`. Lee la doc de QuickGrid antes de hacer la tabla.
> Pega la salida de `dotnet build` al terminar."

**Criterio de dominio:** tienes la misma app funcionando en MVC y en Blazor, y
puedes explicar cuando conviene cada uno.

---

## Fase 4 - TypeScript (semanas 5-6)

Objetivo: entrar al ecosistema donde los agentes de IA son mas fuertes.

Esto es importante por una razon pragmatica: los modelos estan mucho mas afinados
con TypeScript/React que con C#/Blazor. Hay mas documentacion, mas patrones y mas
herramientas. Cuando le pidas a un agente "anade una feature y sus tests", en TS
la calidad sera notable.

### Dia 1-3: Fundamentos de TS
- Tipos, interfaces, genericos, uniones
- `async/await`, `fetch`, `Promise`
- Compilacion a JS, por que existe el tipo

### Dia 4-8: React
- Componentes, props, state, eventos
- Hooks: `useState`, `useEffect`
- Listas y renderizado condicional
- Formularios

### Dia 9-12: Next.js (opcional pero recomendado)
- App Router
- Server Components (la tendencia "vanguardista": renderizado en servidor voltou)
- Fetching de datos

### Ejercicio
Expone tu API de la Fase 2 como API REST limpia y consume desde una SPA de React.

**Practica de agente:**
> "Aqui esta `[mi-api]`. Crea un cliente TypeScript tipado para estos endpoints
> en `[carpeta]`. Incluye manejo de errores tipado. Ejecuta `npm run build` y
> pega la salida real."

**Criterio de dominio:** haces una SPA que consume tu API, con tipos en ambos
lados, y entiendes el flujo completo.

---

## Fase 5 - Produccion (semana 7)

Objetivo: que tu cosa viva fuera de tu maquina.

### Lo que aprendes
- **Docker**: `Dockerfile` multi-stage para .NET
- **Aspire**: orquestacion local de multiples servicios (contendor .NET oficial)
- **CI con GitHub Actions**: build + test en cada push
- **Despliegue**: Azure App Service o container
- **Observabilidad**: logging estructurado, health checks

### Quality gates (esto es Nivel 5 de la guia)
Configura en CI, en este orden:
1. `dotnet build` - si falla, bloquea
2. `dotnet test` - si falla, bloquea
3. Formateo/lint - si falla, bloquea
4. Revision con agente - advertencias

> Nota de .NET 10: `dotnet test` ahora soporta **Microsoft.Testing.Platform (MTP)**.
> Revisa si tu version de xUnit lo soporta; si hay conflictos, actualiza.

**Practica de agente (Nivel 5):**
> "Revisa el diff de este PR. Busca: SQL injection, secretos hardcodeados,
> falta de validacion, y errores de autorizacion. Devuelve solo hallazgos
> con severidad. No devuelvas el diff completo."

**Criterio de dominio:** tu repo tiene CI verde, y un PR generado por agente
pasa los gates.

---

## Fase 6 - IA integrada + agentes (semana 8)

Objetivo: cerrar el circulo. No solo usas agentes para trabajar, tambien metes IA
dentro de tus productos.

### Parte A: IA en la aplicacion
- Llamar a un modelo LLM desde C# (`Microsoft.Extensions.AI` o directamente HTTP)
- **Embeddings y busqueda vectorial**: aqui entra todo lo que viste en EF Core 10.
  Con `vector` + `VECTOR_DISTANCE()` tienes busqueda semantica en SQL Server,
  sin montar una base de datos vector aparte.
- **RAG**: recuperas los documentos mas similares por significado, no por palabra
  clave. Con lo de la Fase 2, lo construyes con consultas LINQ normales.
- Herramientas (function calling): que el modelo ejecute acciones en tu app
- Costes y latencia: como medir y controlar

```csharp
// Contexto: buscar los 3 posts mas similares semanticamente
var resultados = await context.Posts
    .OrderBy(p => EF.Functions.VectorDistance("cosine", p.Embedding, consulta))
    .Take(3)
    .ToListAsync();
```
 Ese resultado es el contexto que le pasas al modelo. Ahi tienes RAG completo,
 con tu base de datos SQL Server de siempre.

### Parte B: Agentes en tu flujo
Aqui aplicas los 6 niveles de la guia:
- Tu `AGENTS.md` ya existe (Fase 2)
- Skills para tus tareas frecuentes (crear endpoint, crear test, revisar PR)
- Hooks que fuerzan `dotnet build` + `dotnet test` antes de commit
- Un subagente de revision de seguridad

**Criterio de dominio:** puedes responder con numeros: "mi agente de review
ahorra X horas, cuesta Y, y falla en Z% de los casos de forma monitorizada".

---

## Calendario resumen

| Semana | Foco | Entregable |
|---|---|---|
| 0 | Entorno | .NET 10, Git, Docker verificados |
| 1 | Git + xUnit | 10 tests, repo limpio |
| 2 | EF Core 10 | API con migraciones y SQL Server |
| 3-4 | Blazor Web Apps | Misma app en componentes |
| 5-6 | TypeScript + React | SPA consumiendo tu API |
| 7 | Docker + CI + despliegue | App en produccion con gates |
| 8 | IA en la app + agentes | RAG/llamadas LLM + agentes en tu flujo |

## Regla de oro del plan

Cada semana, aplica el nivel de agente correspondiente mientras practicas:

| Semana | Nivel de agente |
|---|---|
| 1-2 | Nivel 1-2 (fundamentos, Planear/Implementar/Verificar) |
| 3-4 | Nivel 3 (AGENTS.md, git, tests) |
| 5-6 | Nivel 3-4 (contexto, subagentes) |
| 7 | Nivel 5 (produccion, gates) |
| 8 | Nivel 6 (medicion, coste) |

## Cuando algo salga mal

- El agente se equivoca mucho -> vuelve al Nivel 1. No es tu culpa, es falta de base.
- No entiendes el codigo que genero -> eso es fallo tuyo de revision. Nivel 2.
- Se pierde entre archivos -> tu `AGENTS.md` esta incompleto. Nivel 3.

---

Volver a [README](README.md) · [Plan general de agentes](00-plan-de-ruta.md)