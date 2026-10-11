# Plantillas listas para copiar

## 1. AGENTS.md

Copia este archivo a la raiz de tu proyecto y personalizalo. Borra lo que no
aplique.

```markdown
# AGENTS.md -Instrucciones para agentes de IA

## Que es este proyecto
[1-2 lineas: nombre y proposito]

## Stack
- Lenguaje/Framework: [X] [version]
- Runtime: [Node version / Python version]
- Base de datos: [X]
- Tests: [framework]

## Comandos comunes
- Instalar dependencias: `[comando]`
- Ejecutar en dev: `[comando]`
- Tests: `[comando]`
- Typecheck/lint: `[comando]`
- Build: `[comando]`

## Estructura
- `[carpeta1]/` - [que va ahi, ejemplo de archivo]
- `[carpeta2]/` - [que va ahi]
- ...

## Convenciones de codigo
- [Regla concreta 1 + por que]
- [Regla concreta 2 + por que]
- Estilo: [ejemplo real de codigo correcto]
  ```[lang]
  [codigo de ejemplo, 5-8 lineas]
  ```

## Que NO hacer
- No anadir dependencias sin preguntar antes.
- No tocar [ruta sensible].
- No usar [practica prohibida] porque [motivo].
- No dejar console.log; usa el logger.
```

---

## 2. Prompts base

### Planificar antes de codificar
```
[OBJETIVO]. Antes de escribir codigo, lee [ARCHIVOS] y propon un plan en N pasos
numerados. Indicame que archivos tocaria y que decisiones tecnicas tomarias.
NO escribas codigo todavia.
```

### Ejecutar un paso acotado
```
Ejecuta solo el paso [N]. Archivo: [RUTA]. Nada mas, no toques otros archivos.
```

### Verificacion forzada
```
Ejecuta [COMANDO] y pega la salida REAL y completa. Si no lo has ejecutado,
di explicitamente "no ejecutado". No digas que algo funciona sin evidencia.
```

### Anti-drift de alcance
```
Alcance estricto: solo modifica [ARCHIVO]. Si crees que otros archivos necesitan
cambios, describelos pero NO los hagas.
```

### Auto-critica antes de entregar
```
Antes de declarar la tarea terminada:
1. Ejecuta los tests y pega la salida real.
2. Lista que supuestos hiciste.
3. Lista que casos NO cubriste.
4. Confirma que solo tocaste los archivos permitidos.
```

### Explorar antes de actuar
```
Antes de modificar nada, mapea como funciona [MODULO]: flujo de datos, funciones
publicas, dependencias. Dame un diagrama en texto con rutas. No escribas codigo.
```

### Test primero
```
Escribe un test para [FUNCION] que falle con la implementacion actual (test en
rojo primero). Nombre: `debe [resultado] cuando [condicion]`. Despues implementa
lo minimo para que pase.
```

### Diagnostico sin cambiar codigo
```
Llevas [N] intentos. Para de cambiar codigo. Escribe:
1. Que has intentado.
2. Que error persiste (pega el mensaje EXACTO).
3. Tu hipotesis de por que.
No modifiques nada todavia.
```

---

## 3. Checklist de revision de un cambio de IA

```
[ ] Alcance: solo los archivos permitidos? (git diff --stat)
[ ] Complejidad: hay abstraccion innecesaria?
[ ] Alucinacion: todas las APIs/imports existen de verdad?
[ ] Test: hay test, y fallaria si rompiera la funcion?
[ ] Verificacion ejecutada: pegaste la salida real?
[ ] Entiendo TODO el codigo? Si no, no hago commit.
```

---

## 4. Ejemplo de AGENTS.md para Node/TypeScript
(Por si no tienes proyecto, usa este como base)
```markdown
# API de tareas (Node + TypeScript)

## Stack
- Node 24, TypeScript 5 (strict: true), Express 4
- Tests: Vitest

## Comandos
- `npm install`
- `npm run dev` - servidor con reload
- `npm run test` - tests
- `npm run typecheck` - tsc --noEmit

## Estructura
- `src/routes/` - solo endpoints
- `src/services/` - logica de negocio
- `tests/` - un test por funcion

## Reglas
- Regla de dependencia en una direccion: routes -> services, nunca al reves.
- Validacion con zod en routes.
- Errores como clases de `src/errors.ts`.
- No `any`. strict esta activo a proposito.
```