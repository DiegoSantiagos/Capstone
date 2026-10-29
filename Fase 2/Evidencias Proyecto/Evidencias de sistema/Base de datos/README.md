# Evidencia de sistema — Base de datos

Modelo físico de la base de datos de **Acredittia** sobre **PostgreSQL 16**, obtenido del
código del repositorio `Backend_Acredita` (`app/backend/app/models.py`, SQLAlchemy 2.x).

## Resumen del modelo

| Indicador | Valor |
|---|---|
| Motor | PostgreSQL 16 (Azure Database for PostgreSQL Flexible Server en producción) |
| Tablas | 37 |
| Columnas | 395 |
| Claves foráneas | 66 |
| Tablas multi-tenant (con `company_id`) | 25 |
| Versión de esquema | 6 (scripts `01_esquema`, `02_indices`, `03_triggers`, `04_rls`, `06_migracion_v11`) |

## Contenido de esta carpeta

| Archivo | Descripción |
|---|---|
| `Diagramas ER/ER_0_Modelo_completo.png` | Vista general: las 37 tablas con sus PK/FK. Se omite la relación `company_id → companies` en las tablas de negocio para que se pueda leer. |
| `Diagramas ER/ER_1_Empresas_usuarios_suscripcion.png` | Tenants (empresas), usuarios, tokens, auditoría (`actividad`), planes, suscripciones y facturas. |
| `Diagramas ER/ER_2_Contratos_personal_y_requisitos.png` | Faenas, contratos, cargos, plantillas de requisitos, checklist por contrato, sujetos (trabajadores y equipos) y licencias internas (LIM/EMSIPOR). |
| `Diagramas ER/ER_3_Documentos_IA_alertas_reportes.png` | Documentos y sus archivos, revisiones IA y hallazgos, alertas, calendario, reportes y snapshots de cumplimiento. |
| `Diagramas ER/ER_4_Plataformas_mandante_y_boveda_JWE.png` | Plataformas del mandante (SIGA, Workmate, etc.), credenciales cifradas en JWE con su historial de rotación, integraciones y logs de sincronización. |
| `Diagramas ER/fuentes_mermaid/*.mmd` | Fuente editable de cada diagrama (se abre en [mermaid.live](https://mermaid.live)). |
| `Diccionario_de_datos_Acredittia.xlsx` | Diccionario de datos: hoja *Resumen* por tabla y hoja *Columnas* con tipo, PK, FK, nulabilidad y alcance multi-tenant de cada columna. |

## Seguridad a nivel de datos

- **Row-Level Security (RLS) con `FORCE ROW LEVEL SECURITY`.** La API se conecta con el rol
  `acredittia_app` (`NOBYPASSRLS`) y en cada transacción fija `app.company_id`, `app.is_admin`
  y `app.user_id` con `set_config(..., true)`. Así el propio motor filtra las filas de cada empresa,
  aunque una consulta olvide filtrar.
- **Bóveda de credenciales.** `plataforma_credenciales.credencial_jwe` guarda las credenciales
  del mandante cifradas en JWE (RSA-OAEP-256 + A256GCM, clave en Azure Key Vault). El SELECT de esa
  columna está revocado para `acredittia_app`.
- **Pruebas de aislamiento.** `tests/test_tenants.py` comprueba, entre otras cosas, que la empresa B
  no alcanza recursos de la empresa A, que RLS filtra aunque la consulta no filtre y que una sesión
  sin contexto no devuelve filas. La suite completa del backend tiene 206 pruebas y corre contra
  PostgreSQL 16 real, no SQLite.

## Pendiente

Los scripts SQL del esquema (`modelo_datos/01_esquema.sql` … `06_migracion_v11.sql`) **no están en
el repositorio `Backend_Acredita`**, aunque `docker-compose.yml` y los tests los necesitan
(`../modelo_datos`). Hay que subirlos al repo y copiarlos también a esta carpeta, porque son la
evidencia directa del DDL, los triggers y las políticas RLS.
