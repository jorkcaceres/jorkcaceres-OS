# Portal Jorkcáceres

**ID:** JC-001  
**Versión:** 1.0  
**Estado:** Consolidado

## Objetivo

Dar continuidad a la relación con clientes después del contacto inicial mediante una plataforma propia, clara y segura para consultar información y recoger retroalimentación.

El portal complementa el sitio web público: el sitio presenta la oferta y facilita el contacto; el portal sostiene la relación y la trazabilidad operativa.

## Problema que resuelve

Centraliza información que de otro modo quedaría dispersa entre mensajes, archivos y herramientas: clientes, accesos, proyectos, pagos, comprobantes, encuestas y configuraciones operativas.

## Alcance funcional

El activo reúne tres experiencias bajo una misma identidad:

- una experiencia pública, ligera y sin inicio de sesión para encuestas;
- un área autenticada donde cada cliente consulta únicamente sus proyectos, pagos, comprobantes y respuestas;
- un área administrativa para crear, asociar, editar y confirmar información operativa.

## Modelo de información

- **Contacto o cliente:** persona o empresa con nombre, correo, teléfono y empresa; puede existir sin acceso al portal.
- **Usuario del portal:** cuenta autenticada vinculada a un cliente cuando se concede acceso.
- **Proyecto:** trabajo asociado a un cliente, con código automático, servicio, estado, fechas, carpeta compartida y observaciones.
- **Pago:** registro asociado a un proyecto, con código automático, tipo, monto, estado, fecha y comprobante opcional.
- **Respuesta CSAT:** encuesta pública que se asocia automáticamente al cliente cuando el correo coincide.
- **Configuración:** servicios, tipos de pago e imágenes de bienvenida administrables desde la aplicación.

Crear un cliente no implica crearle acceso. El administrador concede o revoca ese acceso de forma explícita y conserva la responsabilidad de modificar la información operativa.

## Arquitectura implementada

- frontend estático de una sola página para navegación, formularios y visualización;
- Supabase para autenticación, base de datos, almacenamiento privado y funciones de servidor;
- GitHub para versionamiento;
- Hostinger para despliegue automático desde `main`.

Esta arquitectura describe la implementación actual. Los proveedores pueden cambiar; las decisiones reutilizables son separar responsabilidades, validar operaciones sensibles en el servidor y verificar cada despliegue.

## Identidad visual implementada

- azul marino `#0D378C` para acciones;
- azul cielo `#6BA5F2` como acento ocasional;
- gris claro `#F2F2F0` para fondos;
- blanco para tarjetas y negro para texto principal;
- Inter Tight como tipografía de referencia.

Las tarjetas permanecen blancas; los indicadores destacados pueden usar azul marino con texto blanco. El footer común conserva el texto «© 2026 Jorkcáceres. Portal para clientes. V1.0.» mientras corresponda a la versión publicada.

## Experiencia validada

- Diseñar primero para móvil y conservar la misma lógica visual en escritorio.
- Preferir tarjetas y jerarquía clara sobre tablas anchas.
- Ajustar los botones a su contenido y evitar acciones redundantes.
- Usar iconos SVG para funciones, evitando símbolos cuya apariencia cambie entre sistemas.
- Hacer clicables los breadcrumbs y mantener una navegación comprensible.
- Diseñar modales con desplazamiento interno y acciones siempre visibles.
- Solicitar únicamente datos que el usuario puede conocer en ese momento.
- Mostrar un estado visible mientras cada acción se procesa.
- Incorporar paginación antes de que el crecimiento vuelva ilegible una lista.

## Seguridad validada

- Mantener RLS activo y basar las políticas en la propiedad real de los datos.
- Validar sesión, rol y reglas de negocio en el servidor para operaciones administrativas.
- No usar metadatos editables por el usuario como fuente de autorización.
- No exponer claves privilegiadas ni secretos en el navegador, el repositorio o conversaciones.
- Proteger formularios públicos y autenticación contra abuso; en la implementación actual se utiliza Cloudflare Turnstile.
- Validar en `submit-csat` el token de Turnstile, el origen, el dominio y los campos antes de insertar una respuesta.
- Guardar comprobantes en almacenamiento privado y entregarlos mediante enlaces firmados de duración limitada.
- Revocar sesiones antes de eliminar usuarios y excluir expresamente las cuentas administrativas de una limpieza.

## Patrones funcionales reutilizables

### Acceso de clientes

Una cuenta temporal se comunica una sola vez, permite copiarse mediante un icono SVG y se cierra con una única acción. Un contacto sin acceso puede recibirlo posteriormente. El cliente puede cambiar su contraseña y el sistema solicita el cambio cuando detecta una clave temporal.

### Proyectos y pagos

Los códigos se generan automáticamente. Los servicios y tipos de pago se administran como catálogos y sus opciones comienzan minimizadas para no sobrecargar la interfaz. La carpeta compartida de un proyecto se abre directamente, sin una acción adicional para copiar el enlace.

Un pago pendiente no solicita fecha ni comprobante; esos campos aparecen y son obligatorios al confirmarlo. Los comprobantes se visualizan y descargan mediante enlaces temporales. En escritorio, la tarjeta prioriza identificación y monto a la izquierda, y estado y comprobante a la derecha; en móvil apila la información sin perder jerarquía.

### Encuesta CSAT

La encuesta es breve, pública y útil para clientes o contactos. El correo permite asociar posteriormente la respuesta.

- **CSAT:** porcentaje de respuestas con satisfacción 4 o 5.
- **Promedio de satisfacción:** media de la escala de 1 a 5.
- **Cumplimiento de expectativas:** porcentaje de «Sí, completamente» y «En gran parte».
- **Intención de recompra:** porcentaje de «Sí».
- **Observación:** campo opcional para contexto cualitativo.

## Operación y despliegue

- Un cambio en `main` activa la publicación automática, pero no se considera disponible hasta verificar producción.
- Los cambios estáticos relevantes incrementan la versión de los recursos para reducir problemas de caché.
- Cada commit representa una unidad coherente de cambio.
- Base de datos, secretos y funciones de servidor tienen un ciclo de despliegue propio que debe coordinarse con el frontend.
- Después de publicar se prueba el flujo modificado y, ante fallos, se revisa el registro de compilación.

## Criterio de terminado

Un cambio está terminado cuando:

- funciona en móvil y escritorio;
- mantiene acciones claras, estados de carga y navegación predecible;
- respeta permisos reales y almacenamiento privado;
- cubre datos vacíos y errores esperados;
- fue probado en producción en el flujo afectado;
- su resultado y alcance se comunicaron con precisión.

## Roadmap

Las siguientes mejoras deben partir de necesidades reales de operación. Cada módulo nuevo debe definir antes de construirse: problema de negocio, usuario, datos, permisos, validaciones del servidor, experiencia móvil, protección contra abuso y criterio de aceptación.

## Aprendizajes

- La seguridad forma parte del diseño del flujo y no se agrega al final.
- La separación entre contacto y usuario evita conceder acceso por accidente.
- Los estados de los datos deben determinar qué campos y acciones son pertinentes.
- Una automatización de despliegue reduce pasos, pero no reemplaza la verificación.
- La experiencia del cliente mejora cuando consulta información clara y la administración conserva el control operativo.

## Relación con otros documentos

- [Activos Digitales](../Activos_Digitales.md)
- [Soluciones Digitales](../capacidades/Soluciones_Digitales.md)
- [Método](../identidad/Metodo.md)
- [Revisión de Conocimiento](../conocimiento/Revision_de_Conocimiento.md)

## Historial de cambios

### 1.0

- Registro inicial del activo y consolidación de los aprendizajes de su creación, desarrollo e implementación.

