# Preparacion del entorno (Windows)

## Diagnostico actual de tu maquina

| Herramienta | Estado | Accion |
|---|---|---|
| Node.js | v24.21.0 OK | Ninguna |
| npm | 11.19.0 OK | Ninguna |
| Git | NO instalado | Requerido desde nivel 3 |
| Python | No instalado | Opcional, si lo usas |

---

## 1. Instalar Git (prioritario)

Lo necesitas para el nivel 3 (control de versiones, red de seguridad del agente).
Es la instalacion mas importante de esta pagina.

### Opcion A: winget (recomendada)
Abre PowerShell y ejecuta:
```powershell
winget install --id Git.Git -e --source winget
```
Cierra y reabre la terminal. Verifica:
```powershell
git --version
```

### Opcion B: instalador manual
1. Descarga Git for Windows desde https://git-scm.com/download/win
2. Instala con las opciones por defecto (asegurate de que "Git from the command
   line" este elegido para el PATH).

### Configuracion inicial (una sola vez)
```powershell
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
git config --global init.defaultBranch main
```

---

## 2. Python (opcional)
Solo si vas a usar agentes con stacks Python.
```powershell
winget install --id Python.Python.3.12 -e --source winget
```
Luego:
```powershell
python -m pip install --upgrade pip
```

---

## 3. Editor recomendado
Visual Studio Code con estas extensiones:
- **Continue** o **Cline** (agentes dentro de VS Code).
- **ESLint** / **Prettier** (formateo, parte de tus gates).

---

## 4. Estructura de proyecto recomendada para tu aprendizaje

Crea esta estructura para practicar (adapta el nombre):
```
mi-proyecto/
  src/               # codigo
  tests/             # tests
  AGENTS.md          # instrucciones del agente (copia de plantillas/)
  .gitignore         # node_modules, .env, dist
  .env.example       # ejemplo de variables de entorno (SIN secretos reales)
  package.json
  notas/progreso.md  # tu registro de aprendizaje
```

Inicializa git:
```powershell
cd mi-proyecto
git init
git add .
git commit -m "chore: setup inicial"
```

---

## 5. Ejercicio de verificacion del entorno
Cuando termines, el agente debe poder:
1. Leer un archivo tuyo.
2. Ejecutar un comando y mostrarte la salida REAL.
3. Hacer un commit que puedas revertir con `git revert`.

Si algo falla, es probablemente un problema de PATH o permisos. Reinicia la
terminal tras instalar.

---

## 6. Copia el AGENTS.md
Cuando llegues al nivel 3, copia `plantillas/AGENTS.md` a la raiz de tu
proyecto y personalizalo. Ese archivo es tu mayor palanca de productividad.

---

Volver a [README](../README.md)