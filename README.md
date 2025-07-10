# SafeNotes – Análisis de Seguridad DevSecOps

SafeNotes es una aplicación Node.js sencilla creada para prácticas de seguridad automatizada en entornos DevSecOps. En este proyecto, se implementa un análisis de dependencias con vulnerabilidades conocidas mediante **OWASP Dependency-Check**, integrado directamente en un pipeline de **GitHub Actions**.

---

## Objetivos del Proyecto

- Simular una aplicación vulnerable con dependencias inseguras (ej. `express@3.0.0`).
- Ejecutar un análisis automatizado de dependencias con OWASP Dependency-Check.
- Integrar el análisis como parte de un workflow CI.
- Subir automáticamente los reportes generados como artefactos.

---

## Tecnología Utilizada

- **Node.js** (v14 o superior)
- **express@3.0.0** (intencionalmente vulnerable)
- **OWASP Dependency-Check** (v8.4.0)
- **GitHub Actions** (CI/CD)

---

## Estructura del Proyecto

```../safenotes/
├── app-report.html
├── contrib
│   └── html.tpl
├── dependency-check-report.html
├── dockerfile
├── node_modules
│   ├── bytes
│   ├── commander
│   ├── connect
│   ├── cookie
│   ├── crc
│   ├── debug
│   ├── express
│   ├── formidable
│   ├── fresh
│   ├── methods
│   ├── mime
│   ├── mkdirp
│   ├── ms
│   ├── pause
│   ├── qs
│   ├── range-parser
│   └── send
├── package.json
├── package-lock.json
├── README.md
├── report.html
├── security-reports
│   ├── baseline-report.html
│   ├── baseline-report.xml
│   └── zap.yaml
└── src
    └── app.js
```

---

## Instalación y Ejecución Local

```bash
# Clonar repositorio
git clone https://github.com/usuario/safenotes.git
cd safenotes

# Instalar dependencias
npm install

# Ejecutar la app
node app.js
```

---

## CI/CD DevSecOps con GitHub Actions
### Este proyecto incluye un pipeline automatizado que:

    1.Instala dependencias.
    2.Descarga y prepara OWASP Dependency-Check.
    3.Ejecuta un escaneo de dependencias.
    4.Publica el reporte como artefacto.

---

## Archivo .github/workflows/ci.yml

```name: DevSecOps CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 📥 Clonar repositorio
        uses: actions/checkout@v3

      - name: 📦 Instalar dependencias Node.js
        run: npm install

      - name: 🧪 Ejecutar pruebas (si existieran)
        run: echo "Sin pruebas definidas aún"

      - name: 🛠️ Descargar y preparar OWASP Dependency-Check
        run: |
          wget https://github.com/jeremylong/DependencyCheck/releases/download/v8.4.0/dependency-check-8.4.0-release.zip
          unzip -q dependency-check-8.4.0-release.zip -d temp
          mv temp/dependency-check-* dependency-check
          chmod +x dependency-check/bin/dependency-check.sh

      - name: 🔍 Ejecutar Dependency-Check (SCA)
        run: |
          ./dependency-check/bin/dependency-check.sh \
            --project "safenotes" \
            --scan . \
            --format "HTML" \
            --out dependency-check-report

      - name: 📤 Subir reporte como artefacto
        uses: actions/upload-artifact@v4
        with:
          name: dependency-check-report
          path: dependency-check-report
```
---

## Vulnerabilidades Detectadas (ejemplo real)
Al usar express@3.0.0, el análisis reporta varias vulnerabilidades conocidas:

    - CVE ID	Descripción	Severidad
    - CVE-2014-6394	IP spoofing por mal manejo de X-Forwarded-For	Alta
    - CVE-2015-1164	Path traversal en rutas	Alta
    - CVE-2015-8855	DoS por procesamiento de JSON grandes	Media
    - CVE-2016-10540	Bypass de autenticación por mal parsing de rutas	Alta

Recomendación: actualizar a express@4.18.2 o superior.

---

## Reporte de Seguridad
El reporte generado por OWASP Dependency-Check se publica como artefacto del workflow.

Puedes descargarlo desde la pestaña Actions en GitHub luego de cada ejecución exitosa.

---

## Buenas Prácticas DevSecOps Aplicadas
    - Seguridad automatizada desde etapas tempranas del desarrollo (Shift Left).
    - Herramienta SCA para prevenir ataques por dependencias inseguras.
    - Integración CI sin afectar la velocidad de desarrollo.