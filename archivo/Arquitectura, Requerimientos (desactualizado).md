# 📚 Plataforma Gamificada de Programación y Desarrollo de Software  
## Documento de Arquitectura – Backoffice Multitenant

---

## 🎯 Estrategia de Multitenancy
La plataforma se implementará bajo un modelo **multitenant**, donde cada **curso** funciona como un tenant independiente.  
- **Tenant = Curso**  
- Cada curso tiene su propio **padrón de alumnos**, **profesor responsable**, **roadmap**, **ranking** y **economía gamificada**.  
- Los datos de cada curso están **aislados** y no pueden ser accedidos por otros cursos.  
- El **backoffice** administra globalmente los tenants y garantiza la correcta aplicación de roles y permisos.

---

## 👥 Roles principales
- **ADMIN**  
  - Acceso total a la plataforma.  
  - Configura parámetros globales (economía, seguridad, políticas).  
  - No puede auto-eliminarse ni quedar la plataforma sin admins activos.  

- **PROFESOR**  
  - Crea y administra cursos, roadmaps y desafíos.  
  - Gestiona el padrón de alumnos de su curso.  
  - Configura reglas pedagógicas (dificultad, obligatoriedad, reintentos).  

- **ALUMNO**  
  - Se inscribe en cursos mediante código de invitación y validación de legajo.  
  - Participa en desafíos, acumula XP/monedas, compite en rankings.  
  - Nunca accede a información de otros alumnos.  

---

## 📌 Requerimientos Funcionales (RF)
- **RF-MULTI-01**: El sistema debe permitir crear y administrar tenants (cursos).  
- **RF-MULTI-02**: Cada tenant debe tener usuarios asociados (profesor + alumnos).  
- **RF-MULTI-03**: Los datos de cada tenant deben estar aislados (ranking, recompensas, desafíos).  
- **RF-MULTI-04**: Los roles y permisos deben aplicarse dentro del tenant correspondiente.  
- **RF-MULTI-05**: El backoffice debe permitir ver métricas globales y por tenant.  
- **RF-MULTI-06**: El alta de alumnos requiere validación de legajo contra padrón del curso.  
- **RF-MULTI-07**: Los profesores pueden regenerar códigos de invitación de curso.  
- **RF-MULTI-08**: Los cursos tienen estados (draft, activo, archivado) con reglas de transición estrictas.  

---

## 📌 Requerimientos No Funcionales (RNF)
- **RNF-MULTI-01**: Seguridad → garantizar aislamiento de datos entre cursos.  
- **RNF-MULTI-02**: Escalabilidad → soportar múltiples cursos activos en paralelo.  
- **RNF-MULTI-03**: Disponibilidad → uptime ≥99,5%.  
- **RNF-MULTI-04**: Rendimiento → consultas por tenant deben responder en <2 segundos.  
- **RNF-MULTI-05**: Usabilidad → interfaz clara para admins y profesores.  
- **RNF-MULTI-06**: Mantenibilidad → ≥80% cobertura en pruebas unitarias.  
- **RNF-MULTI-07**: Compatibilidad → navegadores modernos (Chrome, Firefox, Edge).  
- **RNF-MULTI-08**: Accesibilidad → cumplimiento WCAG 2.1 nivel AA.  

---

## 🏗️ Arquitectura del Backend
- **API Gateway**  
  - Punto único de entrada.  
  - Aplica seguridad (JWT con tenant_id y rol).  
  - Enruta peticiones a microservicios.  

- **Service Discovery (Eureka/Consul)**  
  - Mantiene registro dinámico de microservicios.  
  - El Gateway lo consulta para ubicar instancias activas.  

- **Microservicios principales**  
  - **Usuarios** → gestión de alta, roles, permisos, padrón.  
  - **Cursos** → roadmaps, estados, configuración pedagógica.  
  - **Desafíos** → teóricos y prácticos, con parámetros globales.  
  - **Ranking** → cálculo por curso (tenant).  
  - **Economía gamificada** → XP, monedas, vidas, recompensas.  
  - **Backoffice** → administración global de tenants y métricas.  

- **Base de datos multitenant**  
  - Opción 1: BD compartida con campo `tenant_id`.  
  - Opción 2: BD separada por tenant (más seguro, más costoso).  

- **Config Server**  
  - Centraliza configuración de todos los servicios.  

- **Logs y métricas**  
  - Integración con Prometheus + Grafana o ELK Stack.  

---

## 📊 Diagrama conceptual del Backend Multitenant

```mermaid
flowchart TD
    A[Cliente (Frontend/App)] --> B[API Gateway]
    B --> C[Service Discovery]
    C --> D[Microservicios]
    D --> E[Base de datos multitenant]

    subgraph D [Microservicios]
        D1[Usuarios]
        D2[Cursos]
        D3[Desafíos]
        D4[Ranking]
        D5[Economía gamificada]
        D6[Backoffice]
    end

    E -->|Aislamiento por tenant (curso)| D


📌 Archivos y configuraciones clave


Configuración global (ADMIN)

Archivo de parámetros de economía (XP, monedas, vidas, recompensas).

Configuración de seguridad (JWT, 2FA, políticas de sesión).

Configuración de retención de datos y auditoría.

Roles y permisos

Archivo/tabla roles.json o roles.yml → define los roles (ADMIN, PROFESOR, ALUMNO).

Archivo/tabla permissions.json → mapea qué acciones puede hacer cada rol.

Ejemplo:

json
{
  "ADMIN": ["manage_global_config", "create_course", "delete_course"],
  "PROFESOR": ["create_challenge", "manage_roster", "edit_roadmap"],
  "ALUMNO": ["attempt_challenge", "view_ranking", "redeem_rewards"]
}
Seguridad

Configuración de autenticación → JWT con tenant_id y role.

Configuración de autorización → middleware que valida permisos según rol.

Configuración de auditoría → logs centralizados (acciones de ADMIN y PROFESOR).

Backoffice

Archivos de configuración para métricas globales y por tenant.

Scripts de inicialización (ADMIN inicial, parámetros por defecto).

Configuración de notificaciones y Guided Tour.



📌 Nombre de la arquitectura

Arquitectura de Microservicios Multitenant con Backoffice Centralizado

Microservicios → porque cada módulo (Usuarios, Cursos, Desafíos, Ranking, Economía) está desacoplado.

Multitenant → porque todos los cursos comparten la misma infraestructura, pero con aislamiento lógico por tenant_id.

Backoffice centralizado → porque hay un panel de administración global que gestiona roles, permisos y métricas.