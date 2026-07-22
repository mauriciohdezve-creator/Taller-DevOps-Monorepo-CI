# Estructura del proyecto

Este documento describe la estructura del repositorio "Taller-DevOps-Monorepo-CI". Es un monorepo con un backend en Python (FastAPI) y un frontend servido mediante Nginx, orquestados con Docker Compose y validados mediante un pipeline de CI en GitHub Actions.

---

## Servicios (docker-compose.yaml)

- **backend**: construido desde `./backend`, imagen `taller-devops-backend:alpine`. No expone puertos al host (`expose: 8000`), solo accesible dentro de la red interna `app`. Incluye healthcheck sobre `/api/health`.
- **frontend**: construido desde `./frontend`, imagen `taller-devops-frontend:alpine`. Expone el puerto `3000` (host) mapeado al `8080` (contenedor), corre como filesystem de solo lectura (`read_only: true`) con `tmpfs` para directorios que Nginx necesita escribir.

Ambos servicios comparten la red bridge `app` y ambos aplican `no-new-privileges` como medida de seguridad adicional.

## Carpetas principales

- `.github/workflows/ci.yml` — Pipeline de integración continua. Se ejecuta en cada pull request hacia `main`, construyendo las imágenes y corriendo las pruebas del backend con pytest.
- `backend/` — API en FastAPI con su propia suite de pruebas (`tests/`).
- `frontend/` — Aplicación cliente servida por Nginx en producción.
- `docker-compose.yaml` — Orquesta ambos servicios para desarrollo y CI.
- `.gitignore` — Excluye archivos temporales y sensibles del control de versiones.

## Cómo ejecutar localmente

```bash
docker compose build
docker compose up -d
docker compose run --rm backend pytest
```

Frontend disponible en `http://localhost:3000`.