# 🧪 Ejercicio: Compartiendo archivos entre trabajos con artefactos

## 🎯 Objetivo

Explorar cómo usar artefactos en GitHub Actions para compartir resultados (como archivos de cobertura de test y builds) entre distintos trabajos del mismo flujo.

---

## 🛠 Tareas

### 1. Crea una aplicación React

Desde la raíz del repositorio, ejecuta:

```bash
mkdir 15-artifacts
cd 15-artifacts
npx create-react-app --template typescript react-app
```

Esto generará una aplicación React con TypeScript dentro de `15-artifacts/react-app`.

---

### 2. Crea el archivo `.github/workflows/artifacts.yaml` con el siguiente flujo:

### 📦 Trabajo: `test-build`

- Corre sobre `ubuntu-latest`.
- Define `artifacts/react-app` como directorio de trabajo predeterminado.

#### Steps del job `test-build`:

1. **Checkout code**  
   Descarga el contenido del repositorio para poder ejecutar acciones sobre él.  
   → Usa `actions/checkout@v4`.

2. **Setup Node**  
   Establece el entorno de Node.js en la versión `20.x` para que los comandos como `npm` funcionen correctamente.  
   → Usa `actions/setup-node@v4`.

3. **Install dependencies**  
   Instala las dependencias del proyecto utilizando `npm ci`, que es más eficiente y reproducible que `npm install`.  
   → Comando: `npm ci`

4. **Unit tests**  
   Ejecuta los tests de la aplicación y genera un informe de cobertura.  
   → Comando: `npm run test -- --coverage`

5. **Upload test results**  
   Sube la carpeta `coverage/` generada por los tests como un artefacto llamado `test-coverage`.  
   → Usa `actions/upload-artifact@v4`  
   → Inputs:
   - `name: test-coverage`
   - `path: artifacts/react-app/coverage`

6. **Build code**  
   Genera el código de producción de la aplicación React.  
   → Comando: `npm run build`

7. **Upload build**  
   Sube el resultado de la build como un artefacto llamado `app-build`.  
   → Usa `actions/upload-artifact@v4`  
   → Inputs:
   - `name: app-build`
   - `path: artifacts/react-app/build`

---

### 🚀 Trabajo: `deploy`

- Corre en `ubuntu-latest`.
- Se ejecuta solo si el job `test-build` finaliza correctamente.

#### Step:

1. **Download build files**  
   Descarga el artefacto `app-build` subido en el job anterior para usarlo en una hipotética etapa de despliegue.  
   → Usa `actions/download-artifact@v4`  
   → Inputs:
   - `name: app-build`
   - `path: build`

---

### ✅ Validación final

1. Haz commit y push de los cambios.
2. Ejecuta el workflow desde la UI de GitHub.
3. Verifica:
   - Que los artefactos `test-coverage` y `app-build` se generen y se puedan descargar desde la pestaña *Artifacts* del workflow.
   - Que los pasos se ejecuten correctamente y reflejen el propósito de cada uno según lo descrito arriba.
