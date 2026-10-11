# Registro de progreso

Tu mapa de referencia real. Esta tabla vale mas que cualquier curso.

## Estado del entorno

| Componente | Estado | Version |
|---|---|---|
| winget | OK | 1.29.380 |
| .NET SDK | OK | 10.0.401 |
| Git | OK | 2.55.0.windows.5 |
| Docker Desktop | OK | 29.8.2 |
| Node.js | OK | 24.21.0 |
| npm | OK | 11.19.0 |

---

## Sesiones

| Fecha | Fase | Tarea | Resultado | Que aprendi |
|---|---|---|---|---|
| 2026-10-11 | 0 | Setup inicial: deteccion del entorno | SDK 10.0.100 encontrado, Git y Docker faltan | winget 1.29.380 disponible |
| 2026-10-11 | 0 | Actualizacion SDK a 10.0.401 | OK | `winget upgrade` falla si el paquete no lo instalo winget. Usar `install` |
| 2026-10-11 | 0 | Instalacion Git 2.55.0 + config global | OK | Elegir "Git from the command line" en el PATH |
| 2026-10-11 | 0 | Instalacion Docker Desktop 29.8.2 | OK, requirio reinicio | `docker ps` vacio = funcionando |
| 2026-10-11 | 0 | Verificacion final del entorno | 4/4 OK | - |

## Aprendido en Fase 0

1. `winget upgrade` solo funciona para paquetes que instalo winget. Si instalaste
   .NET por otro metodo, usa `winget install` (el instalador actualiza sobre el
   existente).
2. Las versiones de .NET se instalan side-by-side. `dotnet --list-sdks` mostrando
   varias NO es un error.
3. `docker ps` con salida vacia significa que Docker funciona correctamente.
4. Verificar con el comando real, no asumir. Esto aplica tanto a mi como al agente.

---

## Fases

- [x] **Fase 0** - Entorno (dia 1-2) — COMPLETADA
- [ ] **Fase 1** - Git + xUnit (semana 1)
- [ ] **Fase 2** - EF Core 10 + SQL Server (semana 2)
- [ ] **Fase 3** - Blazor Web Apps (semanas 3-4)
- [ ] **Fase 4** - TypeScript + React (semanas 5-6)
- [ ] **Fase 5** - Docker + CI + despliegue (semana 7)
- [ ] **Fase 6** - IA en la app + agentes (semana 8)

---

## Notas

Anota aqui cualquier comando que funcione, error que aparezca, o concepto
que necesites repasar. Este archivo es tu bitacora.