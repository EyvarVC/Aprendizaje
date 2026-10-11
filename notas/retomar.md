# Punto de corte de sesion

> Este archivo es un "guardar partida". Lo actualizas antes de dejar la
> computadora y mañana solo le dices al agente que lo lea.

---

## Como se usa

**Al terminar una sesion:**
1. Rellena la seccion de abajo
2. `git add . && git commit -m "wip: puntos de corte"`

**Al empezar una sesion nueva:**
> "Lee `notas/progreso.md` y `notas/retomar.md`, continuamos desde donde quedamos"

---

## Estado actual

**Fecha:** 2026-10-11
**Fase:** Fase 0 completada
**Ruta:** C# / ASP.NET Core -> Web moderna

### Donde estoy
- Entorno completo: .NET SDK 10.0.401, Git 2.55.0, Docker 29.8.2
- Solucion `Inventario.slnx` creada
- Proyecto `Inventario.Tests` con xUnit funcionando (1 test de ejemplo pasa)
- `.gitignore` configurado correctamente
- Visual Studio 2026 (v18) + SSMS 22 disponibles

### Lo siguiente (en orden)
1. Verificar que el primer commit existe (`git log --oneline`)
2. Ejercicio Fase 1: `CalculadoraPrecios` con ciclo test rojo -> verde
3. Practicar git: ramas, diff, revert

### Decisiones tomadas
- **NO instalar VS Code por ahora.** Visual Studio es mejor para .NET/Blazor.
  VS Code se considera en la Fase 4 (TypeScript/React).
- **Un solo repositorio**: el proyecto y la guia de aprendizaje viven juntos.
- **Empezar por los tests** (test en rojo primero), no por el codigo.
- **Fase 2 usara Docker** para SQL Server: `mcr.microsoft.com/mssql/server:2025-latest`
  (la version 2025 es necesaria para busqueda vectorial con EF Core 10).

### Pendiente de decidir
- Version del contenedor de SQL Server para la Fase 2
- Si la UI de la Fase 3 va en Blazor o se invierte con React

### Errores que me han pasado (para no repetirlos)
- `winget upgrade` falla si el paquete no fue instalado por winget -> usar `install`
- `dotnet --list-sdks` mostrando varias versiones es NORMAL (side-by-side)
- `docker ps` vacio = Docker funcionando correctamente

---

## Como esta mi relacion con los agentes

Nivel actual: **Nivel 2 (colaboracion basica)** - en progreso.

Disciplina que me funciona: pedir siempre la salida real del comando, nunca
aceptar "los tests pasan" sin ver `dotnet test` ejecutandose.

Ejercicio pendiente del Nivel 1: pedirle a un agente que ejecute un comando y
pegue la salida. Si dice que pasa sin ejecutar, es que necesita una regla en
`AGENTS.md`.