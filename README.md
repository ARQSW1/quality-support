# Quality Support: IT *Support as a service*

*Quality Support* (**QS**) es una empresa que brinda un servicio de mesa de ayuda de IT a pequeñas empresas que no pueden
mantener un sector de sistemas. 

Por un bono mensual se contrata un servicio que incluye

- Base del conocimiento donde los clientes pueden buscar de acceso ilimitado

- Pack de horas personas Expertas que solucionan los problemas

- El exceso de horas se factura a una tarifa diferencial

- Reporte de horas consumidas

- Reporte histórico de horas y facturación

## SITUACION ACTUAL

Cuenta con una plataforma web que es monolítica encargada de realizar las siguientes operaciones-  Autenticación

- Autenticación
- Autorización
- Administración de usuarios y roles
- Facturación
- Administración de base del conocimiento
- Búsqueda de la base del conocimiento
- Alta de tickets
- Asignación de tickets
- Seguimiento de tickets
- Encuestas de satisfacción
- Notificación a clientes
- Reportes internos (satisfacción de clientes, facturación proyectada)
- Notificaciones vía mail

Perfiles o Roles de USUARIO

- Cliente usuario
  o    Crear Tickets
  o    Seguimiento de ticket
  o    Búsqueda de base del conocimiento
- Cliente Administrador
  o    Cambio de perfil de la empresa (datos de facturación, contacto, notificaciones, etc.)
  o    Crear usuarios dentro de una misma empresa
  o    Consulta de facturación
- Experto:
  o    Administrar tickets
  o    Crear/ modificar notas en la base de datos del conocimiento
  o    Reporte de horas consumidas pro cliente
- Administrador
  o    ABM empresas.
  o    ABM usuarios
  o    Administración del flujo de asignación de tickets
  o    Emitir la facturación
  o    Aprobación temas Base del conocimiento
  o    Administración de usuarios expertos

## PROBLEMAS

1. Baja de todos los servicios en implementación (por su naturaleza monolítica).

2. Cambios en la reglamentación impositiva son muy críticos, frecuentes y deben ser implementados en un plazo corto. Esto hace que muchas veces la cadencia de entrega de otros elementos este supeditada a estos cambios más que la satisfacción del cliente.

3. La base de datos del conocimiento consume muchos recursos (especialmente base de datos) que tiene un impacto negativo en la performance del todo el sistema.

4. Los clientes piden que se amplíen las notificaciones a canales como *Slack*, *Discord*, *Teams*, WhatsApp o Telegram.

## TAREAS A REALIZAR

Proponga una arquitectura que solucione estos problemas. Dicha arquitectura debe tener

- Diagrama estructural
- Características (requerimientos no funcionales)
- Decisiones de arquitectura (cosas que SI O SI se deben cumplir)
- Principios de diseño (recomendaciones)

**IMPORTANTE:**

- Estamos obviando un tema que veremos en ARQ2 que es *Sharding* que es dividir o separar el sistema en varias instancias, por ejemplo: una por cliente.
- Puede cambiar el tipo de motor de base de datos justificando ese cambio o al menos analizando sus ventajas y desventajas.
- Puede separar la base de datos siempre y cuando el sistema conserve la cohesión / sinergia

## SOLUCION PROPUESTA

![Diagrama estructural](./diagrama.drawio.svg)

PROBLEMAS 1, 2, 3 : La una arquitectura de micro servicios. Esto permite el desarrollo, implementación y mantenimiento independiente.

**SUB-DOMINIOS CORE**

- Facturación
- Base del conocimiento
- Tickets

**SUB-DOMINIOS SUPPORTING**

- Autorización
- Notificaciones

**SUB-DOMINIOS GENERIC**

- Bases de datos
- Bus de mensajes
- OIDC Identity Provider

### SEGURIDAD

Al separar el sistema monolítico es necesario tener una experiencia de autenticación y autorización homogénea.

**AUTORIZACION** utilizar OIDC permite utilizar SSO y también incorporar ID federados. Es decir que los clientes se puedan autenticar con credenciales de GMAIL, Outlook , etc.

**AUTORIZACIÓN**: Se genera un nuevo servicio de autorización, que tiene una interface y permite administrar los usuarios y roles en TODOS los sistemas de manera centralizada.

Al autorización en los demás sistemas se encuentra a nivel INTERFACE (API o SPA)

- API esto es una buena práctica para que la capa de negocio sea agnóstica de la seguridad
- SPA : inhabilitar elementos a los que el usuario no tiene autorización mejora la experiencia de usuario

### NOTIFICACIONES

Problema 4, Se opta por una arquitectura de microkernel a fin de poder extender los canales de notificacion sin alterar a los existentes.

### DESVENTAJAS

- Migración de los usuario al OIDC Identity Provider

- LATENCIA EN LA AUTORIZACION , se recomienda una CHACHE de corta duración

- Estar atento a componentes compartidos tanto en la UI como en la capa de negocio encapsularlos en una librerias. Ejemplo la capa de mensajería (ya esta separado en el diagrama)

### POSIBILIDAD DE MEJORA

Utilizar un sistema de reporting genérico como jasper-reports. Ahora no se incluye