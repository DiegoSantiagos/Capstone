
# Evidencias del Proyecto APT122
## Caso Acredittia


Acredittia

Tipo: Desarrollo · · Modalidad: Nuevo · Carpeta: Acredittia

Objetivo de negocio

Acredittia es una plataforma SaaS de gestión de acreditaciones de empresas contratistas, personal y equipos para
faenas mineras y energéticas en Chile. Para trabajar en una faena (Los Pelambres y otras del ecosistema AMSA,
entre las 9 ya integradas), cada empresa proveedora debe acreditar decenas de documentos —contratos,
exámenes de altura, inducciones, F30-1, SOAP, revisiones técnicas, licencias internas de mina (EMSIPOR/LIM)— con
vigencias distintas y en plataformas de mandante diferentes (SIGA, Workmate, Metacontratas, Webcontrol).

Acredittia centraliza ese proceso: checklists documentales autogenerados, revisión automática de documentos con IA, alertas de vencimiento y sincronización con las plataformas del mandante. El diferenciador declarado en el roadmap no es solo la IA, sino la integración profunda por faena (procesos, formularios y contactos), difícil de
replicar.

### Cómo funciona

Una empresa contratista se registra y es aprobada por un administrador; luego crea contratos por faena y da de
alta trabajadores y equipos, para los que el sistema instancia automáticamente los requisitos desde plantillas
maestras (10 ítems de empresa, 13 de personal, 10 de equipo y 9 de EMSIPOR para conductores). Los archivos se
suben directamente a Azure Blob Storage mediante SAS URLs, sin pasar por la API; un worker ejecuta OCR/LLM y
valida cada documento contra la plantilla del requisito (campos clave, vigencia, RUT, firma, legibilidad), clasificando
hallazgos como error, warning o info. Un cron diario recalcula estados (ok → por vencer → vencido), genera alertas
y notifica por email y WhatsApp. La plataforma incluye dashboard de KPIs con tendencia histórica, matriz de
cumplimiento, calendario, reportes PDF/Excel, extracción IA que pre-llena formularios desde contratos,
credenciales de plataformas del mandante cifradas en JWE y auditoría completa de la actividad.

### Componentes

API REST FastAPI (154 endpoints especificados; 46 implementados según el Anexo A, punto de partida real del
equipo); frontend web SPA nuevo derivado del wireframe funcional acredittia.html; workers asíncronos para
revisión IA, reportes y sincronizaciones; conectores de integración (SIGA como primer conector real); catálogos
maestros (faenas, plataformas, cargos, laboratorios, talleres, proveedores GPS); y módulos de alertas, calendario,
reportes y actividad.

### Tecnología

Backend en Python 3.12+ con FastAPI y Pydantic v2; PostgreSQL 16 con SQLAlchemy 

2.x async y migraciones

Alembic (incluye RLS a nivel de fila y multi-tenancy por company_id); Azure Blob Storage con SAS URLs; Celery (o
ARQ) + Redis para tareas asíncronas; Azure Key Vault para secretos y cifrado JWE (RSA-OAEP-256 + A256GCM) de
credenciales; JWT RS256 con refresh tokens; frontend React/Next.js; notificaciones por SMTP/SendGrid y
WhatsApp Business API. Plan de desarrollo de 3 meses: 6 sprints quincenales, 4 personas (BE1, BE2, FE, QA),
priorización MoSCoW, GitHub Actions, pytest y Playwright.
37


## Estructura:

<details>
<summary>Ver estructura de carpetas</summary>

```bash
Evidencias APT122
│
├── Fase 1
│   ├── Evidencias Individuales
│   │   ├── Apellido_Nombre_1.1_APT122_AutoevaluacionCompetenciasFase1.docx
│   │   ├── Apellido_Nombre_1.2_APT122_DiarioReflexionFase1.docx
│   │   └── Apellido_Nombre_1.3_APT122_AutoevaluacionFase1.docx
│   │
│   └── Evidencias Grupales
│       ├── Presentación Proyecto.pptx
│       ├── 1.4_APT122_FormativaFase1.docx
│       ├── 1.5_GuiaEstudiante_Fase 1_Definicion Proyecto APT (Español).docx
│       ├── 1.5_GuiaEstudiante_Fase 1_Definicion Proyecto APT (Inglés).docx  #Optativo
│       └── PLANILLA DE EVALUACIÓN FASE 1.xlsx  (Enviada por correo)
│
├── Fase 2
│   ├── Evidencias Individuales
│   │   └── Apellido_Nombre_2.1_APT122_DiarioReflexionFase2.docx
│   │
│   ├── Evidencias Grupales
│   │   ├── 2.4_GuiaEstudiante_Fase 2_DesarrolloProyecto APT (Español).docx
│   │   ├── 2.4_GuiaEstudiante_Fase 2_DesarrolloProyecto APT (Inglés).docx #Optativo
│   │   ├── PLANILLA DE EVALUACION AVANCE FASE 2.xlsx
│   │   ├── 2.6_GuiaEstudiante_Fase 2_Informe Final Proyecto APT (Español).docx
│   │   ├── 2.6_GuiaEstudiante_Fase 2_Informe Final Proyecto APT (Inglés).docx  #Optativo
│   │   └── PLANILLA DE EVALUACION FINAL FASE 2.xlsx
│   │
│   └── Evidencias Proyecto
│       ├── Presentación Proyecto.pptx
│       ├── Evidencias de documentación
│       └── Evidencias de sistema
│           ├── Aplicación
│           └── Base de datos
│
└── Fase 3
    ├── Evidencias Individuales
    │   └── Apellido_Nombre_3.1_APT122_DiarioReflexionFase3.docx
    │
    └── Evidencias Grupales
        ├── PLANILLA DE EVALUACIÓN FASE 3.xlsx
        ├── Presentación Final del proyecto (Español).pptx
        └── Presentación Final del proyecto (Inglés).pptx  #Optativo
```

</details>