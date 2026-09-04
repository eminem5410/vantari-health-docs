# 🏥 Vantari Health

### Plataforma SaaS multi-tenant para gestión clínica, interoperabilidad y seguridad en salud

Vantari Health es una plataforma **HealthTech de gestión clínica y sanitaria** diseñada para instituciones de salud públicas y privadas.

Su objetivo es digitalizar y conectar los distintos puntos de atención de una red sanitaria —**CAPS, hospitales, laboratorios, farmacia y especialistas**— manteniendo la identidad del paciente, su información clínica, la seguridad de las decisiones, la interoperabilidad y la trazabilidad de las operaciones.

> **Paciente → CAPS → Hospital → Laboratorio → Farmacia → Especialista**

Vantari no busca simplemente reemplazar formularios de papel por pantallas. El objetivo es construir una **infraestructura digital clínica interoperable**, donde la información acompañe al paciente y donde el sistema pueda aplicar reglas de seguridad contextual antes de que ocurran errores.

---

## 📋 Tabla de contenidos

* [Descripción](#-descripción)
* [Estado del proyecto](#-estado-del-proyecto)
* [Características principales](#-características-principales)
* [Seguridad clínica](#-seguridad-clínica)
* [Interoperabilidad FHIR](#-interoperabilidad-fhir)
* [Arquitectura](#️-arquitectura)
* [Control de acceso](#-control-de-acceso)
* [Módulos actuales](#-módulos-actuales)
* [Stack tecnológico](#️-stack-tecnológico)
* [Roadmap](#️-roadmap)
* [Estructura del proyecto](#-estructura-del-proyecto)
* [Instalación](#-instalación)
* [Estado de testing](#-estado-de-testing)
* [Visión](#-visión)

---

# 📖 Descripción

Vantari está orientado a resolver problemas frecuentes de los sistemas sanitarios fragmentados:

* Información clínica distribuida entre papel, Excel y sistemas aislados.
* Falta de continuidad entre CAPS y hospitales.
* Información de alergias o antecedentes que no llega correctamente al siguiente profesional.
* Procesos administrativos manuales.
* Dificultad para gestionar turnos y listas de espera.
* Falta de trazabilidad sobre quién accedió o modificó información clínica.
* Falta de mecanismos automáticos de seguridad clínica.
* Dificultad para generar información estadística y operativa en tiempo real.

La plataforma utiliza una arquitectura **multi-tenant**, permitiendo que distintas instituciones puedan operar de manera aislada dentro de una misma infraestructura.

La información clínica se mantiene estructurada y preparada para interoperabilidad mediante **FHIR R4 / HL7**.

---

# 🚦 Estado del proyecto

## 🟢 Pre-producción / plataforma funcional

El núcleo de Vantari se encuentra actualmente operativo y en desarrollo activo.

### Backend

* API REST construida con FastAPI.
* PostgreSQL con arquitectura multi-tenant mediante schemas.
* Autenticación mediante JWT.
* RBAC basado en roles y permisos.
* ABAC para control de acceso contextual.
* Reglas clínicas.
* Validación de matrícula profesional.
* Auditoría de operaciones.
* Gestión de pacientes.
* Gestión de encuentros clínicos.
* Observaciones y signos vitales.
* Condiciones y alergias.
* Medicaciones y prescripciones.
* Seguridad de prescripción.
* Agenda y turnos.
* Lista de espera.
* Derivaciones.
* Farmacia e inventario.
* Reportes y exportaciones.
* Métricas operativas.
* Interoperabilidad FHIR R4.

### Frontend

Aplicación web construida con Next.js y TypeScript.

Actualmente incluye:

* Dashboard.
* Login.
* Gestión de pacientes.
* Detalle clínico del paciente.
* Clinical Workbench.
* Encuentros.
* Observaciones.
* Profesionales.
* Agenda y turnos.
* Lista de espera.
* Derivaciones.
* Farmacia.
* Auditoría.
* Reportes.
* Prescripción electrónica.
* Interfaz consciente de permisos.

---

# ⭐ Características principales

## 👤 Gestión de pacientes

Permite administrar la identidad y datos básicos del paciente, incluyendo:

* Datos demográficos.
* Documento de identidad.
* Información de contacto.
* Estado del paciente.
* Historial clínico.
* Alergias.
* Condiciones.
* Medicación.
* Observaciones.
* Encuentros.

### Búsqueda clínica tolerante

La búsqueda de pacientes permite localizar personas mediante:

* DNI completo.
* DNI con formato (`30.123.456`).
* DNI parcial/prefijo.
* Nombre.
* Apellido.
* Búsquedas sin necesidad de ingresar correctamente las tildes.

Ejemplo:

```text
Perez     → Pérez
Gomez     → Gómez
Fernandez → Fernández
```

---

# 🩺 Clinical Workbench

El **Clinical Workbench** concentra la información clínica más importante del paciente en una vista ejecutiva.

El objetivo es permitir que un profesional pueda comprender el estado general del paciente en segundos.

Actualmente presenta:

* 🔴 Alertas de alergias de alta criticidad.
* ⚠️ Condiciones activas.
* 💊 Medicación activa.
* 📋 Última atención.
* Información clínica relevante.

La interfaz evita depender de múltiples pantallas para obtener el contexto básico del paciente.

---

# 🛡️ Seguridad clínica

Uno de los objetivos principales de Vantari es que el sistema no sea únicamente un repositorio de información, sino que pueda **intervenir cuando detecta un riesgo clínico**.

## Momento G5 — Seguridad de prescripción

Vantari implementa reglas clínicas capaces de detectar conflictos entre medicamentos y alergias.

Por ejemplo:

```text
Paciente:
Alergia → Penicilina

Profesional:
Prescribe → Amoxicilina

        ↓

Motor de reglas clínicas

        ↓

ALLERGY_CONFLICT

        ↓

🔴 PRESCRIPCIÓN BLOQUEADA
```

El sistema contempla relaciones de familia farmacológica y no únicamente coincidencias textuales exactas.

Actualmente existen reglas relacionadas con familias como:

* Penicilínicos.
* Cefalosporinas.
* Sulfonamidas.
* AINEs.

La prescripción se somete a evaluación antes de ser firmada.

---

# 🔐 Control de acceso RBAC + ABAC

Vantari combina dos mecanismos de autorización.

## RBAC

El sistema utiliza roles y permisos para determinar qué operaciones puede realizar cada usuario.

Entre los roles contemplados se encuentran:

* `admin`
* `medico`
* `director_medico`
* `enfermeria`
* `administrativo`
* `bioquimico`
* `trabajador_social`
* `farmacia`

El sistema actualmente cuenta con un catálogo de permisos granular.

Ejemplos:

```text
patients.read
patients.write
encounters.read
encounters.write
observations.read
observations.write
medications.read
prescriptions.write
clinical.sign
appointments.manage
reports.read
admin.users
```

## ABAC

El RBAC se complementa con **Attribute-Based Access Control**.

Esto permite que un usuario pueda tener un permiso general pero que la operación sea restringida según el contexto.

Ejemplo:

```text
Enfermería
    ↓
encounters.write
    ↓
encounter_type
    ↓
permitido:
    nursing
    control

otro tipo
    ↓
403 Forbidden
```

El motor ABAC contempla:

* Evaluación contextual.
* Políticas almacenadas en PostgreSQL.
* Resolución de atributos.
* Evaluación por prioridad.
* Deny override.
* Auditoría de accesos denegados.

---

# 📜 Auditoría

Las operaciones relevantes son registradas mediante un sistema de auditoría.

La auditoría permite mantener trazabilidad sobre eventos como:

* Creación de recursos.
* Modificaciones.
* Visualización.
* Cambios de estado.
* Acciones clínicas.
* Denegaciones ABAC.

Las denegaciones de autorización contextual se registran específicamente como:

```text
action = abac_deny
```

Esto permite investigar posteriormente quién intentó realizar una operación, sobre qué recurso y bajo qué contexto.

---

# 🏥 Derivaciones CAPS → Hospital

Vantari implementa un flujo completo de derivaciones clínicas.

El objetivo es reemplazar el tradicional:

```text
CAPS
  ↓
papel escrito a mano
  ↓
paciente
  ↓
hospital
```

por:

```text
CAPS
  ↓
Vantari
  ↓
Resumen clínico estructurado
  ↓
Hospital
```

Una derivación puede incluir un resumen clínico generado automáticamente a partir de la información disponible del paciente.

El resumen puede incorporar:

* Alergias.
* Condiciones.
* Medicación activa.
* Encuentros recientes.
* Información clínica relevante.

El flujo actualmente contempla:

```text
draft
  ↓
sent
  ↓
received
  ↓
closed
```

Además, las operaciones quedan registradas mediante auditoría.

---

# 📅 Agenda, turnos y lista de espera

El backend cuenta con infraestructura para:

* Plantillas de horarios.
* Generación de slots.
* Reservas.
* Liberación de turnos.
* Lista de espera.
* Gestión de citas.

La agenda permite trabajar con:

* Profesionales.
* Fechas.
* Horarios.
* Slots disponibles.
* Pacientes.
* Lista de espera.

El objetivo es evitar que la gestión de turnos dependa de identificadores internos o procesos manuales.

---

# 💊 Farmacia

Vantari incorpora infraestructura para gestión de farmacia e inventario.

Incluye:

* Stock.
* Lotes.
* Vencimientos.
* Alertas.
* Movimientos de inventario.
* Dispensación.
* Seguimiento de transacciones.

Esto permite avanzar hacia una conexión entre:

```text
Prescripción
      ↓
Farmacia
      ↓
Stock
      ↓
Dispensación
```

reduciendo la separación entre el acto médico y la disponibilidad real del medicamento.

---

# 📊 Dashboard y métricas

El dashboard consume métricas reales del backend.

Entre las métricas disponibles se encuentran:

* Pacientes.
* Profesionales.
* Encuentros.
* Turnos.
* Medicamentos.
* Prescripciones.
* Alergias críticas.
* Actividad mensual.

La arquitectura permite ampliar posteriormente estas métricas hacia indicadores operativos y sanitarios de una institución o red.

---

# 🔬 Interoperabilidad FHIR

Vantari utiliza **FHIR R4 (HL7)** como capa de interoperabilidad.

FHIR no constituye el dominio interno completo del sistema.

El enfoque utilizado es:

```text
Dominio Vantari
       ↓
Domain Model
       ↓
FHIR Mapper
       ↓
FHIR R4
```

Esto permite mantener un modelo de dominio orientado a las necesidades de la aplicación mientras FHIR funciona como una interfaz estandarizada de intercambio.

Entre los recursos trabajados se encuentran:

* Patient
* Practitioner
* Encounter
* Observation
* Condition
* AllergyIntolerance
* Medication
* MedicationRequest
* Composition
* Bundle
* CapabilityStatement

La arquitectura está preparada para continuar ampliando la cobertura FHIR.

---

# 🏗️ Arquitectura

Vantari utiliza un enfoque **Domain-First**.

La arquitectura separa:

```text
Frontend
    ↓
REST API
    ↓
Application Services
    ↓
Domain
    ↓
Persistence
    ↓
PostgreSQL
```

Con una capa adicional de interoperabilidad:

```text
Domain
   ↓
FHIR Mappers
   ↓
FHIR R4
```

Y una capa transversal de seguridad:

```text
Request
   ↓
Authentication
   ↓
RBAC
   ↓
ABAC
   ↓
Clinical Rules
   ↓
Audit
```

---

# 🏢 Multi-tenancy

Vantari está diseñado como plataforma SaaS multi-tenant.

Cada institución puede operar dentro de un contexto aislado.

La arquitectura utiliza **PostgreSQL schemas** para separar los datos de cada tenant.

Conceptualmente:

```text
Vantari
│
├── Tenant A
│   └── PostgreSQL schema
│
├── Tenant B
│   └── PostgreSQL schema
│
└── Tenant C
    └── PostgreSQL schema
```

Esto permite evolucionar hacia escenarios donde diferentes instituciones de una red sanitaria puedan operar de forma independiente y, posteriormente, establecer mecanismos controlados de interoperabilidad.

---

# 🛠️ Stack tecnológico

## Frontend

* Next.js
* React
* TypeScript
* Tailwind CSS
* TanStack Query
* shadcn/ui

## Backend

* Python
* FastAPI
* SQLAlchemy
* Alembic
* Pydantic

## Base de datos

* PostgreSQL
* Arquitectura multi-tenant mediante schemas.

## Interoperabilidad

* HL7 FHIR R4

## Infraestructura

* Docker
* Docker Compose
* Linux
* VPS / Cloud infrastructure

## Seguridad

* JWT
* RBAC
* ABAC
* Auditoría
* Reglas clínicas

---

# 🧪 Estado de testing

El proyecto cuenta con una suite automatizada de más de **2.400 tests** entre backend, dominio, FHIR, reglas clínicas, API y seguridad.

Las pruebas cubren, entre otros:

* Unit testing.
* Domain testing.
* API testing.
* FHIR.
* Reglas clínicas.
* Aislamiento multi-tenant.
* Seguridad.
* Auditoría.
* RBAC.
* ABAC.
* Flujos de derivación.
* Endpoints clínicos.

El proyecto continúa en proceso de hardening y ampliación de cobertura.

---

# 🗺️ Roadmap

El desarrollo se organiza mediante fases y sprints incrementales.

Actualmente el proyecto se encuentra en una etapa de **pre-producción**, con el núcleo clínico y administrativo operativo.

Las próximas áreas de evolución incluyen:

### Seguridad y hardening

* Ampliación de controles de seguridad.
* Hardening de infraestructura.
* Monitoreo.
* Backup y recuperación.
* Pruebas de carga.
* Seguridad operacional.

### Triage digital

Clasificación estructurada del riesgo del paciente al ingreso, con posibilidad de utilizar:

* Signos vitales.
* Motivo de consulta.
* Variables clínicas.
* Reglas de priorización.

### Plantillas clínicas

Plantillas rápidas para:

* Prescripciones.
* Órdenes.
* Consultas frecuentes.
* Procedimientos.

### Evolución futura

* Integración entre instituciones.
* Portal del paciente.
* Notificaciones mediante canales de alta disponibilidad.
* Integraciones con laboratorios.
* Interoperabilidad ampliada.
* Analítica sanitaria.
* Alertas epidemiológicas.
* Telemedicina.
* Aplicaciones móviles.

El detalle actualizado del desarrollo se encuentra en:

**[`ROADMAP.md`](ROADMAP.md)**

---

# 📁 Estructura del proyecto

```text
vantari-health/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── domain/
│   │   ├── services/
│   │   └── ...
│   │
│   └── tests/
│
├── frontend/
│   └── src/
│       ├── app/
│       ├── components/
│       ├── hooks/
│       ├── lib/
│       └── store/
│
├── docs/
│
├── docker-compose.yml
├── ROADMAP.md
└── README.md
```

---

# 🐳 Instalación

## Requisitos

* Docker
* Docker Compose
* Node.js
* npm
* Python 3.11+

## Clonar el repositorio

```bash
git clone git@github.com:eminem5410/vantari-health.git
cd vantari-health
```

## Variables de entorno

Configurar las variables necesarias para backend y frontend.

Ejemplo:

```bash
cp frontend/.env.local.example frontend/.env.local
```

Configurar posteriormente:

```text
backend/.env
frontend/.env.local
```

## Levantar infraestructura

```bash
docker-compose up -d
```

El procedimiento completo de instalación y configuración puede ampliarse en la documentación técnica del proyecto.

---

# 🎯 Visión

La visión de Vantari es evolucionar desde un sistema de gestión clínica hacia una **infraestructura digital para redes sanitarias**.

El objetivo final es que una persona pueda ser atendida en distintos puntos de una red y que el contexto clínico relevante pueda acompañarla de forma segura:

```text
                 ┌──────────────┐
                 │    Vantari   │
                 └──────┬───────┘
                        │
       ┌────────────────┼────────────────┐
       │                │                │
     CAPS            Hospital         Laboratorio
       │                │                │
       └────────────────┼────────────────┘
                        │
                     Farmacia
                        │
                   Especialistas
```

La plataforma busca combinar:

**Interoperabilidad + Seguridad clínica + Gestión sanitaria + Trazabilidad + Datos**

en una única infraestructura.

---

## 📌 Estado actual

> **Vantari Health — Pre-producción**
>
> Núcleo clínico operativo · Multi-tenant · FHIR R4 · RBAC + ABAC · Clinical Workbench · Seguridad de prescripción · Agenda · Derivaciones · Farmacia · Auditoría

---

## 📄 Licencia

Este proyecto se encuentra actualmente en desarrollo.

La información sobre licencia y condiciones de uso será definida antes de una distribución pública de la plataforma.

---

**Vantari Health**
*Infraestructura digital para una salud conectada, segura e interoperable.*
