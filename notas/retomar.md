# Punto de corte de sesion

> Este archivo es tu "guardar partida". Lo actualizas antes de dejar la
> computadora y al volver solo le dices al agente que lo lea.

---

## COMO RETOMAR

**Sesion nueva, una sola frase:**
> "Lee `notas/progreso.md` y `notas/retomar.md`, continuamos desde donde quedamos"

**Antes de dejar la computadora:**
1. Edita la seccion "Donde estoy" / "Lo siguiente" de este archivo
2. En la terminal: `git add . && git commit -m "wip: puntos de corte"`

La sesion de chat no importa. Los archivos son la memoria.

---

## Estado: 2026-10-11 (tarde)

**Fase:** Fase 0 COMPLETADA. Entrando a Fase 1.
**Ruta:** C# / ASP.NET Core -> Web moderna (8 semanas)

### Donde estoy exactamente

**Completado y verificado:**
- .NET SDK **10.0.401**
- Git **2.55.0.windows.5** (config global: Eyvar / gmail / rama main)
- Docker **29.8.2** (`docker ps` vacio = OK)
- Visual Studio **2026 (v18)**
- SQL Server Management Studio **22**
- VS Code **1.141.0** + extension **C# Dev Kit** + sesion iniciada con GitHub
- Solucion `Inventario.slnx` creada
- Proyecto `Inventario.Tests` con xUnit funcionando
- `.gitignore` funcionando (verificado: no aparecen bin/ ni obj/)
- **2 commits** en git

**PENDIENTE - nada de esto se ha ejecutado todavia:**
- [ ] Crear `Inventario.Core` (libreria de clase)
- [ ] Conectar `Inventario.Tests` -> `Inventario.Core`
- [ ] Escribir el test rojo de `CalculadoraPrecios`
- [ ] Verificar que el test FALLA
- [ ] Pedir implementacion al agente
- [ ] Verificar que el test PASA

### Lo siguiente: paso a paso

**Paso 1** (en la terminal de VS Code, `Ctrl+`` ):
```powershell
dotnet new classlib -o Inventario.Core
dotnet sln Inventario.slnx add Inventario.Core\Inventario.Core.csproj
dotnet add Inventario.Tests\Inventario.Tests.csproj reference Inventario.Core\Inventario.Core.csproj
```

**Paso 2:** abrir `Inventario.Tests\UnitTest1.cs`, borrar todo y pegar el
contenido que esta en `notas/Ejercicio1-Calculadora.md` (test con 5 escenarios).

**Paso 3:** `dotnet test` -> DEBE FALLAR. ese error es el objetivo.

**Paso 4:** pedirle al agente que implemente `CalculadoraPrecios`.

**Paso 5:** `dotnet test` -> debe pasar.

**Paso 6:** `git add . && git commit -m "feat: calculadora de precios con tests"`

---

### Decisiones tomadas (no volver a preguntar)
- **NO instalar mas editores.** Visual Studio para .NET/Blazor. VS Code ya
  instalado para notas + terminal + TypeScript en Fase 4.
- **Un solo repositorio**: proyecto + guia de aprendizaje + notas juntos.
- **Siempre test primero** (test rojo antes de implementar). El agente
  implementa TU diseño, no el suyo.
- **Fase 2 usara Docker** con `mcr.microsoft.com/mssql/server:2025-latest`
  (necesario para busqueda vectorial con EF Core 10).

### Errores ya que me han pasado
- `winget upgrade` falla si el paquete no lo instalo winget -> usar `install`
- `dotnet --list-sdks` con varias versiones es NORMAL (side-by-side)
- `docker ps` vacio = funcionando bien
- Avisos de "LF will be replaced by CRLF" en git = normal en Windows

### Disciplina con agentes
Nivel actual: **2 (colaboracion basica)**.

Regla que me esta funcionando: **siempre pedir la salida real del comando.**
Nunca aceptar "los tests pasan" sin ver `dotnet test` ejecutandose.

Ejercicio pendiente del Nivel 1: cuando el agente implemente `CalculadoraPrecios`,
comprobar si ejecuta `dotnet test` o si solo dice que pasa.

---

## Atajos utiles

| Atajo | Que hace |
|---|---|
| `Ctrl+`` ` `` | Terminal integrada |
| `Ctrl+Shift+P` | Paleta de comandos (busca aqui si no encuentras algo) |
| `Ctrl+Shift+V` | Preview de Markdown |
| `Ctrl+K V` | Markdown lado a lado |
| `Ctrl+Shift+E` | Explorador |
| `Ctrl+Shift+G` | Panel de Git |
| `Alt+Z` | Ajustar linea a pantalla |

## Comandos que uso seguido

```powershell
cd D:\Eyvar\PruebasIA\Aprendizaje
dotnet test                    # correr tests
dotnet test --filter "Nombre"  # correr un solo test
git log --oneline              # historial
git status                     # cambios pendientes
git diff                       # ver cambios linea por linea
git switch -c rama-nueva       # crear rama
```