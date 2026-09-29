# Caso funcional — Gestión de interrupciones por grupo de trabajo

> **Caso anonimizado para portafolio profesional.**  
> El nombre real del cliente y las personas involucradas fueron reemplazados por nombres genéricos.

## 1. Contexto

Una empresa de servicios con diferentes **centros/unidades de operación** administra grupos de colaboradores que no comparten necesariamente los mismos tipos de permisos o interrupciones.

Actualmente, la asociación entre interrupciones y grupos requiere una configuración técnica que no puede ser gestionada directamente por el cliente desde la aplicación.

### Problema identificado

El sistema permite visualizar interrupciones sin aplicar correctamente la segmentación por grupo de trabajo. Esto puede provocar que:

- Un programador visualice interrupciones que no corresponden a su grupo.
- Un empleado pueda seleccionar permisos que no aplican a su unidad.
- El cliente dependa de una configuración técnica para modificar la relación entre interrupciones y grupos.

### Objetivo funcional

Permitir que las interrupciones puedan asociarse a grupos de trabajo y que la aplicación filtre automáticamente las interrupciones disponibles de acuerdo con el grupo al que pertenece el usuario.

---

# 2. Actores

| Actor | Responsabilidad |
|---|---|
| Administrador del cliente | Crea/configura interrupciones y las asocia a grupos |
| Programador de turnos | Programa personal y asigna interrupciones |
| Empleado / Portal | Solicita permisos o interrupciones |
| Analista Funcional | Levanta, documenta y valida el requerimiento |
| Equipo de Desarrollo | Diseña e implementa la solución |
| QA | Valida el comportamiento funcional |

---

# 3. Historias de Usuario

## HU-01 — Asociación de interrupciones a grupos

**Como** administrador del cliente,  
**quiero** asociar una interrupción a uno o varios grupos de trabajo,  
**para** definir qué grupos pueden utilizar o visualizar cada tipo de interrupción.

### Criterios de aceptación

**CA-01.1 — Crear asociación**

- Dado que el administrador tiene permisos de configuración,
- cuando cree o edite una interrupción,
- entonces el sistema debe permitir seleccionar el grupo o grupos de trabajo a los que aplica.

**CA-01.2 — Asociación obligatoria**

- Dado que se está configurando una interrupción,
- cuando el administrador intente guardar la configuración sin grupo asociado,
- entonces el sistema debe informar que debe existir al menos una asociación válida.

**CA-01.3 — Múltiples grupos**

- Dado que una interrupción aplica a diferentes grupos,
- cuando el administrador seleccione varios grupos,
- entonces la interrupción debe quedar disponible para todos los grupos seleccionados.

**CA-01.4 — Independencia entre grupos**

- Dado que existen diferentes grupos de trabajo,
- cuando una interrupción sea asociada únicamente al Grupo A,
- entonces dicha interrupción no debe estar disponible para el Grupo B.

**CA-01.5 — Gestión desde la aplicación**

- Dado que el administrador requiere modificar la segmentación,
- cuando acceda a la funcionalidad de configuración,
- entonces debe poder realizar la asociación desde la aplicación sin requerir intervención directa sobre la base de datos.

---

## HU-02 — Visualización de interrupciones según grupo del programador

**Como** programador de turnos,  
**quiero** visualizar únicamente las interrupciones asociadas a los grupos que tengo autorizados,  
**para** evitar asignar permisos que no corresponden al personal que estoy programando.

### Criterios de aceptación

**CA-02.1 — Filtrado por grupo**

- Dado que el programador tiene asignado el Grupo A,
- cuando ingrese a la funcionalidad de asignación de interrupciones,
- entonces el sistema debe mostrar únicamente las interrupciones asociadas al Grupo A.

**CA-02.2 — Exclusión de interrupciones**

- Dado que una interrupción está asociada al Grupo B y no al Grupo A,
- cuando el programador del Grupo A consulte las interrupciones,
- entonces la interrupción del Grupo B no debe aparecer.

**CA-02.3 — Programador con varios grupos**

- Dado que un programador tiene autorización sobre los Grupos A y B,
- cuando consulte las interrupciones,
- entonces debe visualizar las interrupciones asociadas a cualquiera de los grupos que tiene autorizados.

**CA-02.4 — Aplicación durante la asignación**

- Dado que el programador selecciona un colaborador perteneciente a un grupo,
- cuando consulte las interrupciones disponibles,
- entonces el sistema debe aplicar la segmentación correspondiente al grupo del colaborador.

**CA-02.5 — No alterar permisos existentes**

- La nueva funcionalidad no debe modificar la configuración de grupos que ya tiene asignada cada usuario programador.

---

## HU-03 — Visualización de interrupciones en el portal del empleado

**Como** empleado,  
**quiero** visualizar únicamente los tipos de interrupción que aplican a mi grupo de trabajo,  
**para** solicitar permisos válidos de acuerdo con las reglas de mi unidad de operación.

### Criterios de aceptación

**CA-03.1 — Identificación del grupo**

- Dado que el empleado está asociado a un grupo mediante su enrolamiento,
- cuando ingrese al portal de empleados,
- entonces el sistema debe identificar automáticamente su grupo de trabajo.

**CA-03.2 — Filtrado de permisos**

- Dado que el empleado pertenece al Grupo A,
- cuando solicite un permiso,
- entonces únicamente deben mostrarse las interrupciones asociadas al Grupo A.

**CA-03.3 — Exclusión de otros grupos**

- Dado que una interrupción pertenece exclusivamente al Grupo B,
- cuando un empleado del Grupo A consulte los tipos de permiso,
- entonces dicha interrupción no debe estar disponible.

**CA-03.4 — Solicitud válida**

- Dado que el empleado selecciona una interrupción disponible para su grupo,
- cuando registre la solicitud,
- entonces el sistema debe permitir continuar con el flujo normal de solicitud.

**CA-03.5 — Consistencia de la segmentación**

- La segmentación aplicada en el portal debe corresponder a la misma configuración de asociación entre interrupciones y grupos definida por el administrador.

---

# 4. Reglas de negocio

### RN-01 — Asociación por grupo

Cada interrupción debe poder relacionarse con uno o varios grupos de trabajo.

### RN-02 — Visibilidad condicionada

Un usuario solo debe visualizar interrupciones que sean válidas para el grupo correspondiente según su rol y asociación.

### RN-03 — Programador de turnos

El programador solo debe gestionar interrupciones correspondientes a los grupos que tiene autorizados.

### RN-04 — Portal de empleados

El empleado solo debe solicitar interrupciones disponibles para su grupo de trabajo.

### RN-05 — Configuración administrable

La relación entre interrupciones y grupos debe poder administrarse funcionalmente desde la aplicación, evitando depender de cambios directos en base de datos.

### RN-06 — Integridad

La implementación no debe modificar las asociaciones existentes entre usuarios y grupos.

---

# 5. Escenarios funcionales principales

| Escenario | Resultado esperado |
|---|---|
| Administrador asocia interrupción al Grupo A | La interrupción queda disponible para Grupo A |
| Administrador asocia interrupción a A y B | La interrupción queda disponible para ambos grupos |
| Programador de Grupo A consulta interrupciones | Solo visualiza interrupciones válidas para A |
| Empleado de Grupo A solicita permiso | Solo visualiza permisos válidos para A |
| Interrupción pertenece a Grupo B | No debe aparecer para usuarios de Grupo A |
| Administrador modifica asociación | La nueva segmentación se aplica sin intervención técnica sobre BD |

---

# 6. Alcance

## Incluye

- Asociación de interrupciones a grupos.
- Consulta de asociaciones existentes.
- Segmentación de interrupciones para programadores.
- Segmentación de interrupciones para empleados.
- Validación funcional de los diferentes escenarios.
- Conservación de las asociaciones existentes entre usuarios y grupos.

## No incluye

- Modificación de la estructura de base de datos.
- Rediseño del módulo de programación de turnos.
- Cambios en el proceso de enrolamiento.
- Cambios en las reglas de cálculo de nómina.
- Creación de nuevos tipos de interrupción fuera del alcance de esta funcionalidad.

---

# 7. Trazabilidad funcional

**Necesidad del negocio → Requerimiento → Historias de usuario → Criterios de aceptación → Pruebas funcionales → UAT**

La principal decisión funcional fue separar la necesidad en tres capacidades:

1. **Parametrizar** la relación interrupción–grupo.
2. **Aplicar** la segmentación al programador de turnos.
3. **Aplicar** la misma segmentación al portal del empleado.

Esto permite mantener trazabilidad entre la necesidad planteada por el cliente y los escenarios que deben ser validados por QA/UAT.

---

# 8. Evidencia del análisis funcional

El requerimiento fue identificado durante una sesión de levantamiento en la que se evidenció que la configuración podía realizarse técnicamente mediante base de datos, pero que la necesidad del cliente era poder administrar la relación directamente desde la aplicación.

El análisis funcional permitió convertir esa necesidad operativa en:

- actores y roles;
- reglas de negocio;
- historias de usuario;
- criterios de aceptación;
- escenarios funcionales;
- alcance y exclusiones;
- trazabilidad para pruebas.

> **Nota para portafolio:** Este documento representa un caso práctico anonimizado. No contiene nombres reales de clientes, usuarios, credenciales, datos personales ni información técnica sensible.
