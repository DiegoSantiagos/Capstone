# Evidencia de sistema — Aplicación

Aplicación web de **Acredittia**: API REST en FastAPI, frontend web en Next.js y workers asíncronos.
El código vive en el repositorio `DiegoSantiagos/Backend_Acredita`. En esta carpeta va una copia
(`Codigo_fuente_Acredittia.zip`, sin archivos compilados).

## Despliegues

| Componente | URL |
|---|---|
| API (documentación OpenAPI / Swagger) | https://backend-sigma-acredita.vercel.app/docs |
| Frontend web | https://frontend-nine-khaki-95.vercel.app |

## Estado de la implementación

| Indicador | Valor |
|---|---|
| Endpoints de negocio bajo `/api/v1` | **161** (76 GET · 46 POST · 22 PATCH · 15 DELETE · 2 PUT) |
| Endpoints de salud | 2 (`/health`, `/health/esquema`) |
| Módulos (routers) | 24 |
| Tablas del modelo de datos | 37 (ver carpeta `Base de datos`) |
| Pruebas automatizadas (pytest) | 206, en 10 archivos, contra PostgreSQL 16 real |

El detalle de cada endpoint (método, ruta, módulo y descripción) está en `Catalogo_endpoints_API.xlsx`.

### Funcionalidad implementada

- **Autenticación y cuentas:** registro de empresa con aprobación de un administrador, JWT con
  refresh tokens, restablecimiento de contraseña y roles `admin`, `company` y `contract_admin`.
- **Núcleo de acreditación:** faenas, contratos, cargos y checklists autogenerados desde
  plantillas maestras: 10 requisitos de empresa, 13 de personal, 10 de equipo y 9 EMSIPOR para
  conductores. Incluye personal y equipos (sujetos), además de la licencia interna de mina (LIM/EMSIPOR).
- **Gestión documental:** carga de documentos (Azure Blob Storage con SAS URLs o almacenamiento
  local), versiones de archivo, vencimientos y estados `ok → por vencer → vencido`.
- **Revisión con IA:** revisión del documento contra la plantilla del requisito, con hallazgos
  clasificados como error, warning o info. El modo `simulada` sirve para desarrollo y `claude` usa un LLM real.
- **Operación:** alertas, dashboard de KPIs, calendario, auditoría de actividad, preferencias de
  notificación y reportes PDF/Excel (también programados).
- **Plataformas del mandante:** registro por faena y contrato, y credenciales cifradas en JWE
  (RSA-OAEP-256 + A256GCM) con historial de rotación.
- **Suscripción:** planes, suscripciones, facturas y webhook de pagos.
- **Procesos asíncronos:** Celery + Redis (worker y beat) para la revisión IA, los reportes y el cron
  diario de vencimientos.

### Pendiente (marcado como `TODO` en el código)

- Guardar en Azure Key Vault las credenciales de las integraciones (SIGA, Workmate…). Hoy la
  integración se registra, pero queda `desconectada`.
- Envío real de correos (restablecimiento de contraseña e invitaciones), porque falta configurar SMTP.
- Cobro con pasarela de pagos (Transbank / Stripe). El flujo de suscripción y el webhook ya existen.

## Stack tecnológico

| Capa | Tecnología |
|---|---|
| Backend | Python 3.12, FastAPI, Pydantic v2, SQLAlchemy 2.x, psycopg 3 |
| Base de datos | PostgreSQL 16 con Row-Level Security |
| Asíncrono | Celery 5 + Redis 7 (APScheduler en modo desarrollo) |
| Seguridad | JWT (PyJWT), Argon2 (pwdlib), JWE con `cryptography`, Azure Key Vault |
| Archivos | Azure Blob Storage (SAS URLs) |
| Frontend | Next.js 14.2, React 18.3, Tailwind CSS 3.4, TypeScript |
| Infraestructura | Docker / docker-compose; Azure Container Apps (API, worker, beat y job de migración) |
| Pruebas | pytest + httpx + pgserver (PostgreSQL embebido) |

### Pantallas del frontend

Login · Dashboard · Contratos (listado y detalle) · Personal (listado y ficha) ·
Equipos (listado y ficha) · Faenas · Alertas · Administración.

## Cómo ejecutarlo en local

```bash
cd app
docker compose up --build
# Web: http://localhost:3000   ·   API: http://localhost:8000/docs
```

> **Requisito:** `docker-compose.yml` monta los scripts SQL desde `../modelo_datos/`, y esa
> carpeta todavía no está en el repositorio. Hay que subirla para que el entorno levante desde cero.

## Archivos de esta carpeta

| Archivo | Contenido |
|---|---|
| `README.md` | Este documento |
| `Catalogo_endpoints_API.xlsx` | Resumen por módulo y listado completo de los 161 endpoints |
| `Codigo_fuente_Acredittia.zip` | Copia del código del backend y del frontend |
| `Link_Backend.txt` / `Link_Frontend.txt` | URLs de los despliegues |
