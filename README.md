# 🏥 Vantari Health

### Infraestructura digital para una salud conectada, segura e interoperable.

**Vantari Health** es una plataforma **HealthTech SaaS multi-tenant** para la gestión clínica y sanitaria de instituciones públicas y privadas.

El proyecto busca digitalizar y conectar los distintos puntos de atención de una red sanitaria —**CAPS, hospitales, laboratorios, farmacia y especialistas**— manteniendo la identidad del paciente, su información clínica, la seguridad de las decisiones, la interoperabilidad y la trazabilidad de las operaciones.

> **Paciente → CAPS → Hospital → Laboratorio → Farmacia → Especialista**

Vantari no busca simplemente reemplazar formularios de papel por pantallas. El objetivo es construir una **infraestructura digital clínica interoperable**, capaz de acompañar al paciente a través de una red sanitaria y aplicar mecanismos de seguridad contextual sobre la información clínica.

---

## 📋 Tabla de contenidos

* [¿Qué es Vantari?](#-qué-es-vantari)
* [Problema que busca resolver](#-problema-que-busca-resolver)
* [Características principales](#-características-principales)
* [Seguridad clínica](#-seguridad-clínica)
* [RBAC + ABAC](#-rbac--abac)
* [Clinical Workbench](#-clinical-workbench)
* [Derivaciones interinstitucionales](#-derivaciones-interinstitucionales)
* [Agenda y turnos](#-agenda-y-turnos)
* [Farmacia](#-farmacia)
* [Interoperabilidad FHIR](#-interoperabilidad-fhir)
* [Arquitectura](#️-arquitectura)
* [Multi-tenancy](#-multi-tenancy)
* [Auditoría](#-auditoría)
* [Estado del proyecto](#-estado-del-proyecto)
* [Roadmap](#️-roadmap)
* [Código fuente](#-código-fuente)
* [Contacto](#-contacto)

---

# 📖 ¿Qué es Vantari?

Vantari Health nace con una visión simple:

> **La información clínica debería acompañar al paciente, no quedar atrapada dentro de una institución.**

En muchos sistemas sanitarios, la información se encuentra fragmentada entre:

* Historias clínicas en papel.
* Planillas Excel.
* Sistemas independientes.
* Formularios de derivación.
* Registros administrativos.
* Información que no se comparte entre instituciones.

Vantari propone una arquitectura donde estos procesos puedan formar parte de una misma infraestructura digital.

```text
                         VANTARI HEALTH
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
         CAPS              HOSPITAL          LABORATORIO
          │                   │                   │
          └───────────────────┼───────────────────┘
                              │
                         FARMACIA
                              │
                       ESPECIALISTAS
```

La plataforma combina:

**Gestión clínica + Seguridad + Interoperabilidad + Auditoría + Gestión sanitaria**

---

# 🎯 Problema que busca resolver

Vantari está orientado principalmente a problemas presentes en redes sanitarias donde diferentes instituciones deben compartir información y coordinar la atención.

Entre ellos:

### 🧑‍⚕️ Atención clínica

* Información clínica fragmentada.
* Falta de contexto al atender a un paciente.
* Antecedentes y alergias que pueden no estar disponibles.
* Registros clínicos difíciles de consultar rápidamente.

### 📅 Turnos

* Gestión manual de agendas.
* Sobreturnos sin trazabilidad.
* Listas de espera inexistentes o informales.
* Dificultad para reutilizar turnos cancelados.

### 📨 Derivaciones

* Formularios en papel.
* Información incompleta.
* Falta de comunicación entre CAPS y hospitales.
* Pérdida del contexto clínico durante una derivación.

### 💊 Medicación

* Separación entre prescripción y farmacia.
* Falta de información sobre disponibilidad.
* Riesgo de errores relacionados con alergias.
* Dificultad para conocer si una medicación fue efectivamente dispensada.

### 📊 Gestión sanitaria

* Información estadística generada manualmente.
* Datos distribuidos entre diferentes sistemas.
* Dificultad para obtener métricas operativas en tiempo real.

---

# ⭐ Características principales

## 👤 Gestión de pacientes

Vantari permite administrar:

* Identidad del paciente.
* Documento.
* Datos demográficos.
* Información de contacto.
* Alergias.
* Condiciones.
* Medicación.
* Observaciones.
* Encuentros clínicos.
* Historial de atención.

### 🔎 Búsqueda tolerante

El sistema permite localizar pacientes mediante:

* DNI completo.
* DNI con formato.
* DNI parcial.
* Nombre.
* Apellido.
* Búsquedas sin necesidad de escribir correctamente las tildes.

Por ejemplo:

```text
Perez      → Pérez
Gomez      → Gómez
Fernandez  → Fernández
```

---

# 🩺 Clinical Workbench

El **Clinical Workbench** concentra la información clínica relevante del paciente en una única vista.

El objetivo es permitir que un profesional pueda obtener rápidamente el contexto necesario para una atención segura.

Actualmente contempla:

* 🔴 Alertas de alergias.
* ⚠️ Condiciones activas.
* 💊 Medicación activa.
* 📋 Última atención.
* Información clínica relevante.

La idea central es:

> **"Resumen clínico en 2 segundos."**

---

# 🛡️ Seguridad clínica

Vantari incorpora reglas clínicas que permiten que el sistema no sea solamente un repositorio de información.

El sistema puede **evaluar determinadas acciones antes de permitirlas**.

## 💊 Momento G5 — Seguridad de prescripción

Uno de los flujos principales desarrollados es la detección de conflictos entre medicamentos y alergias.

Ejemplo:

```text
Paciente
   │
   └── Alergia: Penicilina
            │
            ▼
Profesional intenta prescribir
            │
            └── Amoxicilina
                    │
                    ▼
             Motor de reglas
                    │
                    ▼
             ALLERGY_CONFLICT
                    │
                    ▼
             🔴 BLOQUEADO
```

La lógica contempla relaciones de familia farmacológica y no solamente coincidencias exactas de texto.

Entre las familias contempladas actualmente se encuentran:

* Penicilínicos.
* Cefalosporinas.
* Sulfonamidas.
* AINEs.

La prescripción es evaluada antes de su firma clínica.

---

# 🔐 RBAC + ABAC

Vantari utiliza una arquitectura combinada de **Role-Based Access Control (RBAC)** y **Attribute-Based Access Control (ABAC)**.

## RBAC

Determina qué puede hacer cada usuario según su rol y permisos.

Los roles contemplados incluyen:

```text
admin
medico
director_medico
enfermeria
administrativo
bioquimico
trabajador_social
farmacia
```

Los permisos son granulares y están orientados a recursos y operaciones.

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

ABAC agrega una segunda capa:

> **No alcanza con saber quién es el usuario. También importa qué está intentando hacer y sobre qué contexto.**

Por ejemplo:

```text
Enfermería
    │
    └── encounters.write
             │
             ▼
       encounter_type
             │
      ┌──────┴──────┐
      ▼             ▼
   control       nursing
      │             │
      └──────┬──────┘
             ▼
           ALLOW

consulta médica
      │
      ▼
     DENY
      │
      ▼
      403
```

El motor ABAC contempla:

* Políticas contextuales.
* Resolución de atributos.
* Evaluación por prioridad.
* Deny override.
* Políticas almacenadas en base de datos.
* Auditoría de denegaciones.

---

# 📨 Derivaciones interinstitucionales

Vantari incorpora un flujo completo para derivaciones entre instituciones.

El objetivo es transformar:

```text
CAPS
  ↓
papel escrito a mano
  ↓
paciente
  ↓
hospital
```

en:

```text
CAPS
  ↓
Vantari
  ↓
Resumen clínico estructurado
  ↓
Hospital
```

Una derivación puede generar automáticamente un resumen clínico utilizando información existente del paciente.

Puede incluir:

* Alergias.
* Condiciones.
* Medicación activa.
* Encuentros recientes.
* Información clínica relevante.

El flujo contempla:

```text
draft
  ↓
sent
  ↓
received
  ↓
closed
```

Las operaciones también quedan registradas mediante auditoría.

---

# 📅 Agenda y turnos

Vantari cuenta con infraestructura para gestionar agendas y turnos.

Actualmente contempla:

* Plantillas de horarios.
* Generación de slots.
* Disponibilidad.
* Reserva.
* Liberación de turnos.
* Lista de espera.
* Gestión de citas.

El objetivo es que un administrativo pueda trabajar con la agenda sin tener que manipular identificadores internos o procesos manuales.

---

# 💊 Farmacia

El sistema incorpora infraestructura para gestión de farmacia e inventario.

Incluye:

* Stock.
* Lotes.
* Vencimientos.
* Alertas.
* Movimientos de inventario.
* Dispensación.
* Transacciones.

La visión es conectar progresivamente:

```text
Prescripción
     ↓
Farmacia
     ↓
Stock
     ↓
Dispensación
```

permitiendo reducir la separación existente entre la decisión clínica y la disponibilidad real de medicamentos.

---

# 📊 Dashboard y métricas

Vantari incorpora un dashboard basado en métricas reales del backend.

Actualmente permite consultar información como:

* Pacientes.
* Profesionales.
* Encuentros.
* Turnos.
* Medicamentos.
* Prescripciones.
* Alergias críticas.
* Actividad mensual.

La arquitectura permite ampliar estas métricas hacia indicadores operativos, administrativos y sanitarios.

---

# 🔬 Interoperabilidad FHIR

Vantari utiliza **HL7 FHIR R4** como capa de interoperabilidad.

El proyecto utiliza un enfoque **Domain-First**, por lo que FHIR no reemplaza el modelo de dominio interno.

La arquitectura conceptual es:

```text
                 VANTARI DOMAIN
                       │
                       ▼
                  FHIR MAPPERS
                       │
                       ▼
                    FHIR R4
                       │
                       ▼
              External Systems
```

Entre los recursos trabajados se encuentran:

* `Patient`
* `Practitioner`
* `Encounter`
* `Observation`
* `Condition`
* `AllergyIntolerance`
* `Medication`
* `MedicationRequest`
* `Composition`
* `Bundle`
* `CapabilityStatement`

Esto permite que Vantari pueda evolucionar hacia integraciones con otros sistemas sanitarios.

---

# 🏗️ Arquitectura

Vantari utiliza un enfoque **Domain-First**, buscando separar claramente:

```text
Frontend
    │
    ▼
REST API
    │
    ▼
Application Services
    │
    ▼
Domain
    │
    ▼
Persistence
    │
    ▼
PostgreSQL
```

Con capas transversales de:

```text
Authentication
      │
      ▼
RBAC
      │
      ▼
ABAC
      │
      ▼
Clinical Rules
      │
      ▼
Audit
```

Y una capa de interoperabilidad:

```text
Domain
   │
   ▼
FHIR Mappers
   │
   ▼
FHIR R4
```

---

# 🏢 Multi-tenancy

Vantari está diseñado como una plataforma **SaaS multi-tenant**.

La arquitectura utiliza PostgreSQL con separación por schemas para aislar los datos de diferentes tenants.

Conceptualmente:

```text
                    VANTARI
                       │
          ┌────────────┼────────────┐
          │            │            │
       Tenant A     Tenant B     Tenant C
          │            │            │
       Schema A     Schema B     Schema C
```

Esto permite evolucionar hacia escenarios donde diferentes instituciones puedan operar independientemente dentro de la misma plataforma.

La arquitectura también contempla mecanismos controlados de interoperabilidad entre instituciones.

---

# 📜 Auditoría

La trazabilidad es un componente fundamental del sistema.

Vantari registra eventos relevantes relacionados con:

* Creación de recursos.
* Modificaciones.
* Visualizaciones.
* Cambios de estado.
* Acciones clínicas.
* Denegaciones de autorización.
* Operaciones sobre derivaciones.

Las denegaciones ABAC utilizan específicamente:

```text
action = abac_deny
```

Esto permite mantener un historial de las operaciones relevantes y facilitar investigaciones posteriores.

---

# 🧪 Calidad y testing

El proyecto cuenta con una suite automatizada de **más de 2.400 tests**.

Las pruebas cubren diferentes capas del sistema:

* Unit tests.
* Domain tests.
* API tests.
* FHIR.
* Reglas clínicas.
* RBAC.
* ABAC.
* Aislamiento multi-tenant.
* Auditoría.
* Seguridad.
* Derivaciones.
* Endpoints clínicos.

El proyecto continúa en proceso de hardening y ampliación de cobertura.

---

# 🚦 Estado del proyecto

## 🟢 Pre-producción

Vantari cuenta actualmente con un núcleo funcional de gestión clínica y sanitaria.

### Actualmente implementado

* ✅ Gestión de pacientes.
* ✅ Clinical Workbench.
* ✅ Encuentros clínicos.
* ✅ Observaciones.
* ✅ Alergias.
* ✅ Condiciones.
* ✅ Medicaciones.
* ✅ Prescripción electrónica.
* ✅ Reglas de seguridad clínica.
* ✅ Bloqueo por conflicto de alergias.
* ✅ RBAC.
* ✅ ABAC.
* ✅ Auditoría.
* ✅ FHIR R4.
* ✅ Arquitectura multi-tenant.
* ✅ Agenda.
* ✅ Turnos.
* ✅ Lista de espera.
* ✅ Derivaciones.
* ✅ Farmacia.
* ✅ Reportes.
* ✅ Dashboard y métricas.

El proyecto se encuentra actualmente en evolución hacia una etapa de mayor **hardening, validación y preparación para escenarios reales de implementación institucional**.

---

# 🗺️ Roadmap

El desarrollo de Vantari se organiza mediante fases y sprints incrementales.

Las principales áreas actuales y futuras incluyen:

### 🏥 Gestión clínica

* Evolución del Clinical Workbench.
* Historia clínica longitudinal.
* Plantillas clínicas.
* Órdenes y formularios.
* Mejoras en workflows profesionales.

### 🚑 Triage digital

Clasificación estructurada del riesgo al ingreso utilizando información clínica y reglas de priorización.

### 📅 Agenda

* Optimización de agendas.
* Gestión avanzada de turnos.
* Listas de espera.
* Reasignación automática.
* Gestión por especialidad e institución.

### 💊 Farmacia

* Integración más profunda entre prescripción, stock y dispensación.
* Alertas.
* Trazabilidad de medicamentos.
* Gestión avanzada de inventario.

### 🔄 Interoperabilidad

* Ampliación de cobertura FHIR.
* Integración con sistemas externos.
* Intercambio seguro entre instituciones.
* Evolución hacia redes sanitarias interoperables.

### 📊 Gestión sanitaria

* Indicadores operativos.
* Estadísticas sanitarias.
* Dashboards institucionales.
* Alertas epidemiológicas.
* Analítica de red.

### 📱 Acceso del paciente

A futuro se contempla un modelo de comunicación orientado a canales de alta disponibilidad, incluyendo:

* SMS.
* WhatsApp.
* Notificaciones.
* Consulta de turnos.
* Recetas activas.
* Resultados de estudios.

El detalle actualizado del desarrollo se encuentra en el roadmap interno del proyecto.

---

# 🔒 Código fuente

El código fuente completo de **Vantari Health** se mantiene actualmente en un repositorio privado debido a la naturaleza del proyecto y a que involucra procesos relacionados con el ámbito sanitario.

Este repositorio público funciona como espacio de documentación y presentación del proyecto.

Aquí se publica información sobre:

* Arquitectura.
* Visión del producto.
* Funcionalidades.
* Seguridad.
* Interoperabilidad.
* Roadmap.
* Decisiones técnicas.
* Evolución del proyecto.

El código fuente completo, infraestructura y configuraciones internas no forman parte de este repositorio público.

### Acceso técnico

Para evaluaciones profesionales, colaboraciones, revisiones de arquitectura, integraciones o potenciales oportunidades de desarrollo, es posible solicitar una demostración privada o acceso técnico controlado.

---

# 🤝 Colaboración

Vantari se encuentra en desarrollo activo y está abierto a conversaciones con:

* Profesionales de tecnología.
* Arquitectos de software.
* Especialistas en HealthTech.
* Profesionales de salud.
* Instituciones sanitarias.
* Empresas de tecnología.
* Organizaciones interesadas en interoperabilidad.
* Potenciales colaboradores e integradores.

Las contribuciones al código fuente se gestionan actualmente de forma privada.

---

# 📬 Contacto

Si querés conocer Vantari Health en mayor profundidad, discutir una posible integración, revisar la arquitectura o solicitar una demostración:

**Contacto:** Pablo Diez

> Para consultas técnicas o institucionales, contactarse directamente con el responsable del proyecto.

---

# 🏥 Vantari Health

### *Infraestructura digital para una salud conectada, segura e interoperable.*

**Gestión clínica · Seguridad del paciente · Interoperabilidad · Auditoría · Multi-tenancy**
