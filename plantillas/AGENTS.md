# AGENTS.md - Instrucciones para agentes de IA

> Copia este archivo a la raiz de tu proyecto, renombralo `AGENTS.md` y
> personalizalo. Borra las lineas entre `[corchetes]`.

## Que es este proyecto
[1-2 lineas: nombre y para que sirve]

## Stack
- Lenguaje / Framework: [X] [version exacta]
- Runtime: [Node version / Python version]
- Base de datos: [X]
- Tests: [framework y como se ejecutan]
- Gestor de dependencias: [npm / pnpm / yarn / pip]

## Comandos comunes
```bash
# Instalar dependencias
[comando]

# Ejecutar en desarrollo
[comando]

# Ejecutar tests
[comando]

# Typecheck / lint
[comando]

# Build de produccion
[comando]
```

## Estructura del proyecto
```
[carpeta1]/   - [que va aqui]. Ej: [ejemplo de archivo]
[carpeta2]/   - [que va aqui]. Ej: [ejemplo de archivo]
tests/        - [como se organizan los tests]
```

## Convenciones de codigo
- [Regla concreta 1]. [Por que se hace asi].
- [Regla concreta 2]. [Por que].
- [Regla concreta 3].

Estilo de referencia (copiar esta forma):
```[lang]
[5-8 lineas de codigo real que representen el estilo del proyecto]
```

## Formato y commits
- Formateador: [prettier / eslint / rustfmt / ...]
- Mensajes de commit: [convencion, ej: Conventional Commits]
- Ejemplo: `feat(tareas): anadir endpoint de borrado`

## Que NO hacer
- No anadir dependencias sin preguntar antes.
- No modificar [ruta sensible, ej: migraciones de DB].
- No usar [practica prohibida], porque [motivo].
- No dejar `console.log`; usa el logger de `[ruta del logger]`.
- No reescribir archivos que noHas leido antes.
- No hacer refactors no solicitados. Si crees que algo necesita refactor,
  proposalo y espera approval.

## Verificacion obligatoria antes de dar por terminado
```bash
[comando de typecheck]
[comando de tests]
```
- Pega la salida real de estos comandos. No afirmes que pasan si no lo has
  ejecutado.
- Si un comando falla, no digas que la tarea esta terminada.
```