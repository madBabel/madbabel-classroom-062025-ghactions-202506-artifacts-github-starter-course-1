## Ejercicio: Compartiendo archivos entre trabajos con artefactos

###  Objetivo

Explorar cómo usar artefactos en GitHub Actions para compartir resultados entre distintos trabajos del mismo flujo.

---

### Tareas

1. Crea una aplicación React con `create-react-app`:

   - Ejecuta en la raíz del repositorio:
     ```bash
     mkdir 15-artifacts
     cd 15-artifacts
     npx create-react-app --template typescript react-app
     ```

2. Crea el archivo `.github/workflows/artifacts.yaml` con el siguiente comportamiento:

#### 📦 Trabajo: `test-build`

- Corre en `ubuntu-latest`.
- Define `artifacts/react-app` como `working-directory`.
- Contiene los siguientes steps:

  1. **Checkout code**  
     Usa `actions/checkout@v4`.

  2. **Setup Node**  
     Usa Node `20.x`.

  3. **Install dependencies**  
     Ejecuta `npm ci`.

  4. **Unit tests**  
     Ejecuta `npm run test -- --coverage`.

  5. **Upload test results**  
     Usa `actions/upload-artifact@v4` con:
     - `name: test-coverage`
     - `path: artifacts/react-app/coverage`

  6. **Build code**  
     Ejecuta `npm run build`.

  7. **Upload build**  
     Usa `actions/upload-artifact@v4` con:
     - `name: app-build`
     - `path: artifacts/react-app/build`

#### Trabajo: `deploy`

- Corre en `ubuntu-latest`.
- Depende del job `test-build`.
- Contiene un solo step:

  - **Download build files**  
    Usa `actions/download-artifact@v4` con:
    - `name: app-build`
    - `path: build`

3. Haz push y ejecuta el flujo desde GitHub UI.
4. Verifica que los artefactos `test-coverage` y `app-build` se generen y descarguen correctamente.
