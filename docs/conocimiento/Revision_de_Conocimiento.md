# Revisión de Conocimiento

**Versión:** 1.3  
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

### Diagnóstico digital del Portal Jorkcáceres

El Diagnóstico digital validó un recorrido adaptable de seis ejes, un modelo de decisión editable y versionado, y un informe que distingue evidencia, información parcial e insuficiencia antes de priorizar acciones.

### Propiedades CM

Propiedades CM validó patrones para operar un producto privado con permisos por acción, evidencia financiera inmutable, documentos contextuales, auditoría y experiencia mobile first, manteniendo sus reglas de negocio separadas de otros activos.

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

### Adaptar la conversación a la evidencia

Una evaluación no necesita forzar la misma cantidad de preguntas para todos. Las aclaraciones deben aparecer únicamente cuando falta una condición que cambia la calidad del resultado.

### Versionar el criterio

Cuando una solución depende de una metodología editable, cada resultado debe conservar la versión utilizada. Mejorar el criterio no debe reescribir la lógica ni la interpretación de resultados anteriores.

### Separar información vigente y evidencia histórica

Las operaciones nuevas utilizan la información vigente. Los comprobantes y decisiones pasadas se apoyan en snapshots inmutables para no cambiar cuando se actualizan las entidades raíz.

### Diseñar permisos según acciones

Los perfiles generales simplifican la administración, pero la autorización real debe corresponder a acciones concretas. La interfaz reduce ruido; el servidor impone la frontera de seguridad.

### Mantener los documentos en contexto

Los archivos deben vivir dentro de la vivienda, arrendamiento, proyecto o proceso que explica su propósito. Un módulo documental genérico puede aumentar navegación y perder contexto.

### Eliminar también implica almacenamiento

Cuando una operación elimina o reemplaza un archivo, el diseño debe considerar el registro lógico, el objeto físico, los reintentos y la auditoría.

### Demostrar sin revelar producción

Los portafolios y demostraciones utilizan interfaces públicas, datos ficticios o ambientes aislados. La aprobación para mencionar un proyecto no autoriza a publicar sus datos operativos.

### Entregar para continuar

Cuando el cliente necesita modificar el resultado, la entrega incluye fuentes editables o formatos interoperables y recursos organizados. El archivo final no debe ser la única forma de conservar el trabajo.

## Validación

Estos aprendizajes fueron validados mediante el rediseño completo del sitio web, las versiones del Portal Jorkcáceres, su Diagnóstico digital y Propiedades CM. Se consideran aplicables a futuras decisiones, servicios, activos o proyectos.

## Relación con otros documentos

- Filosofía
- Método
- Capacidades
- Lenguaje
- [Portal Jorkcáceres](../activos/Portal_Jorkcaceres.md)
- [Propiedades CM](../activos/Propiedades_CM.md)

## Historial de cambios

### 1.3

- Incorporación de aprendizajes del Diagnóstico digital, Propiedades CM, demostraciones seguras y entregables editables.

### 1.0

- Creación inicial con el aprendizaje validado del rediseño completo del sitio web de Jorkcáceres.

### 1.1

- Incorporación de aprendizajes validados del Portal Jorkcáceres sobre experiencia, permisos, seguridad, estados, crecimiento y despliegue.

### 1.2

- Incorporación de aprendizajes de Portal V1.1 sobre recurrencias, estados calculados, conservación histórica, pagos unificados, filtros reversibles, QA de integración y compatibilidad móvil.


