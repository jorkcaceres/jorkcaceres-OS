# Revisión de Conocimiento

**Versión:** 1.2  
**Estado:** Aprobado

## Propósito

Registra aprendizajes validados que puedan mejorar permanentemente Jorkcáceres OS y orientar futuras decisiones, servicios o proyectos.

## Aprendizaje validado

### Rediseño completo del sitio web de Jorkcáceres

El rediseño completo del sitio web validó, mediante aplicación práctica, principios reutilizables para estructurar la oferta y comunicarla públicamente.

### Creación e implementación del Portal Jorkcáceres

El Portal Jorkcáceres validó principios reutilizables para construir y operar soluciones con experiencias públicas, privadas y administrativas, conservando claridad para el cliente y control operativo para Jorkcáceres.

### Evolución del Portal Jorkcáceres a V1.1

La V1.1 validó cómo ampliar un activo digital desde la gestión de proyectos hacia una relación continua con clientes, incorporando servicios recurrentes, pagos unificados, priorización administrativa y mecanismos de operación escalable.

## Aprendizajes reutilizables

### El problema orienta la solución

El problema de negocio debe orientar la solución antes que la tecnología.

### El resultado orienta la definición

El resultado esperado debe orientar la definición de la solución.

### Estructura interna y comunicación pública

Internamente, Jorkcáceres estructura su oferta mediante capacidades y modalidades.

Públicamente, las capacidades se presentan como servicios y las modalidades pueden presentarse como subservicios o categorías del servicio.

### Comunicación para comprender, decidir y avanzar

El lenguaje público debe ayudar al cliente a comprender, decidir y avanzar, no limitarse a describir capacidades.

### Narrativa según la capacidad

Cada capacidad puede requerir una narrativa y experiencia de comunicación diferente.

### El OS y la experiencia pública

El OS conserva el conocimiento estructural y el sitio web lo traduce a una experiencia pública.

### Comunicación ligera

La comunicación ligera reduce fricción y respeta el tiempo del cliente.

### Seguridad desde el diseño

Los permisos, la propiedad de los datos, la validación del servidor, la protección contra abuso y el almacenamiento privado deben definirse junto con el flujo funcional, no después de construirlo.

### Separar relación, acceso y operación

Un contacto puede existir sin una cuenta. El acceso se concede explícitamente y no transfiere al cliente la responsabilidad de crear, modificar o confirmar información operativa.

### La interfaz responde al estado real

Los formularios deben solicitar solo la información disponible en cada momento. Los estados del proceso determinan qué campos y acciones son pertinentes.

### Automatizar no elimina la verificación

El despliegue automático mejora la continuidad, pero cada cambio debe comprobarse en producción. Frontend, base de datos, secretos y funciones de servidor pueden tener ciclos de despliegue distintos.

### Diseñar para crecimiento y continuidad

La experiencia móvil, la paginación, los estados de carga, los códigos automáticos y una navegación predecible deben considerarse desde el diseño para evitar retrabajo cuando aumenten los usuarios y registros.

### Calcular los estados que dependen del tiempo

Los estados próximos a vencer o vencidos deben derivarse de fechas y configuraciones de alerta. La automatización evita errores manuales y mantiene vigente la información.

### Conservar historia sin mantener operación futura

Inactivar un servicio debe detener renovaciones y cobros futuros sin eliminar los comprobantes ni la información histórica que explica la relación con el cliente.

### Unificar procesos equivalentes

Cuando diferentes módulos producen pagos, una vista financiera común simplifica la consulta y el control sin perder la trazabilidad de origen.

### Configurar sin ocultar

Los filtros predeterminados mejoran la operación diaria, pero deben ser reversibles. «Mostrar todo» permite recuperar la vista completa y evita que una configuración oculte información involuntariamente.

### Probar integraciones como flujos completos

Frontend, RLS, Edge Functions, Storage y CORS deben verificarse juntos cuando participan en una misma acción. Una prueba aislada de cada componente no garantiza el resultado para el usuario.

### Validar en dispositivos reales

Los controles nativos, especialmente fechas y archivos, pueden variar entre navegadores y dispositivos. La validación debe incluir el entorno móvil utilizado por el cliente.

## Validación

Estos aprendizajes fueron validados mediante el rediseño completo del sitio web y las versiones 1.0 y 1.1 del Portal Jorkcáceres. Se consideran aplicables a futuras decisiones, servicios, activos o proyectos.

## Relación con otros documentos

- Filosofía
- Método
- Capacidades
- Lenguaje
- [Portal Jorkcáceres](../activos/Portal_Jorkcaceres.md)

## Historial de cambios

### 1.0

- Creación inicial con el aprendizaje validado del rediseño completo del sitio web de Jorkcáceres.

### 1.1

- Incorporación de aprendizajes validados del Portal Jorkcáceres sobre experiencia, permisos, seguridad, estados, crecimiento y despliegue.

### 1.2

- Incorporación de aprendizajes de Portal V1.1 sobre recurrencias, estados calculados, conservación histórica, pagos unificados, filtros reversibles, QA de integración y compatibilidad móvil.


