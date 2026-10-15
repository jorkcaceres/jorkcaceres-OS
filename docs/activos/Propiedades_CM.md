# Propiedades CM

**ID:** JC-002  
**Versión:** 0.6.2  
**Estado:** Producción operativa

## Objetivo

Centralizar la administración de viviendas arrendadas, relaciones contractuales, pagos recibidos y comprobantes verificables mediante una aplicación privada, segura y adaptable a móvil y escritorio.

Propiedades CM nació para reemplazar registros manuales y recibos elaborados de forma aislada. Su valor principal es conservar una fuente confiable de información, evidencia y decisiones sobre la operación de las propiedades.

## Frontera del activo

Propiedades CM es un producto independiente del Portal Jorkcáceres y de los demás activos de la organización. Puede reutilizar patrones técnicos, de seguridad y de experiencia, pero conserva su propio modelo de negocio, sus entidades y sus reglas operativas.

La versión vigente atiende una sola organización. No es todavía una plataforma SaaS multiempresa y no utiliza datos reales de producción como material de demostración.

## Problema que resuelve

- reúne información antes dispersa por vivienda, persona, arrendamiento y pago;
- conserva trazabilidad desde el arrendamiento hasta el recibo emitido;
- genera comprobantes PNG consistentes y verificables mediante un código aleatorio;
- permite delegar tareas sin compartir la cuenta administradora;
- vincula referencias y documentos con la vivienda o el arrendamiento que les da contexto;
- registra quién realizó cada operación y qué cambió.

## Alcance funcional

### Inicio

Presenta únicamente los módulos permitidos para la cuenta y los datos principales de la sesión.

### Arrendadores y arrendatarios

Permite crear, consultar, buscar, editar, inactivar y reactivar personas vinculadas a la operación. La inactivación conserva la historia y evita la eliminación física ordinaria.

### Viviendas

Administra datos del inmueble, características, arrendador vigente, referencias y documentos. Las referencias se expresan mediante nombre, identificación y entidad. Los documentos admitidos son PDF de hasta 20 MB y permanecen en almacenamiento privado.

### Arrendamientos

Relaciona una vivienda con un arrendatario titular y conserva el arrendador asociado. Solo puede existir un arrendamiento activo por vivienda. Cuando ya existe un pago, las condiciones que sustentan la evidencia histórica quedan protegidas.

### Pagos

Registra el dinero recibido, su concepto, fecha, valor, medio, referencia, pagador y nota interna. La operación utiliza una clave de idempotencia para evitar duplicados al reintentar el mismo formulario.

El sistema registra la fecha real del ingreso. No asigna pagos a meses o periodos y no calcula cartera, mora ni intereses.

### Recibos

Cada pago puede tener como máximo un recibo. El comprobante se genera como PNG a partir de un snapshot protegido y utiliza un código `PCM` acompañado de 32 caracteres hexadecimales aleatorios.

El recibo acredita un valor recibido, pero no declara paz y salvo. La anulación conserva el pago, el recibo y su motivo; la verificación oficial prevalece sobre cualquier PNG compartido previamente.

### Usuarios y permisos

Una cuenta confirmada en Supabase Auth no obtiene acceso por sí sola. También requiere una membresía activa en Propiedades CM. El administrador autoriza a cada persona y define permisos por módulo y por acción.

Los perfiles de apoyo son Administrador, Operador, Consulta y Personalizado. Un gestor no puede conceder permisos superiores a los propios, modificar su propio estado ni alterar administradores. El último administrador activo está protegido frente a suspensión accidental.

### Actividad

Mantiene un historial paginado de operaciones con responsable, entidad, acción y estados anterior y posterior.

## Reglas transversales

- Los listados muestran 10 registros por página, del más reciente al más antiguo.
- Las ediciones comprueban una versión esperada para detectar cambios concurrentes.
- Arrendadores, arrendatarios, viviendas y arrendamientos se inactivan en lugar de eliminarse.
- Referencias y documentos admiten eliminación controlada cuando la operación lo requiere.
- La experiencia es mobile first y conserva paridad funcional en escritorio.
- Los selectores de formularios consultan el conjunto autorizado y no quedan limitados por la paginación visible.

## Modelo de información

- **Miembro:** cuenta autorizada, estado, perfil y permisos.
- **Arrendador:** persona responsable vigente de una o más viviendas.
- **Arrendatario:** persona titular de un arrendamiento.
- **Vivienda:** inmueble, ubicación, características, arrendador vigente y estado.
- **Referencia:** identificador operativo asociado a una vivienda.
- **Arrendamiento:** relación entre vivienda, titular, arrendador, canon y fechas.
- **Pago:** ingreso confirmado asociado a un arrendamiento.
- **Recibo:** comprobante único vinculado a un pago.
- **Documento:** PDF privado contextualizado dentro de vivienda o arrendamiento.
- **Actividad:** evidencia de la operación realizada.

## Información vigente y evidencia histórica

Las pantallas operativas consultan la información vigente desde sus entidades raíz. Al registrar un pago, la solución toma la información actual y congela en un snapshot inmutable los datos necesarios para el comprobante.

Esta separación evita dos errores: utilizar información desactualizada en operaciones nuevas y reescribir comprobantes históricos cuando cambian personas, viviendas o relaciones. El dato vigente gobierna la creación; el snapshot gobierna la evidencia pasada.

## Arquitectura implementada

- Next.js 16.4.0, React 19 y TypeScript para aplicación, rutas privadas y renderizado de recibos;
- Supabase Auth para identidad y sesiones;
- Supabase PostgreSQL para entidades, permisos, auditoría y reglas transaccionales;
- Supabase Storage privado para documentos PDF;
- Cloudflare Turnstile para proteger el inicio de sesión;
- Hostinger para ejecución de la aplicación y dominio HTTPS;
- GitHub como fuente del código y del despliegue.

La rama `develop` concentra el trabajo y `main` representa producción. La aplicación expone `/api/health` para comprobar versión y habilitación del acceso.

## Seguridad y privacidad

- La sesión utiliza cookies HttpOnly, Secure en producción y SameSite Lax.
- Cada operación sensible valida sesión, membresía activa y permiso concreto en el servidor.
- La base de datos utiliza RLS, privilegios mínimos y controles de integridad.
- Los documentos permanecen en un bucket privado y se consultan mediante URL firmada de corta duración.
- Los PDF se validan por tipo MIME y firma del archivo.
- Los recibos usan códigos aleatorios no secuenciales.
- La verificación pública muestra únicamente código, valor, concepto, fecha, emisión y estado de anulación.
- El repositorio no conserva secretos, datos personales ni comprobantes reales.
- La clave secreta de Supabase existe solo en el servidor y nunca utiliza el prefijo `NEXT_PUBLIC`.

## Identidad y experiencia

- amarillo como color principal de marca en acceso y barra superior;
- logo blanco completo sobre amarillo y favicon basado en la forma de la vivienda;
- navegación y acciones visibles según los permisos;
- tarjetas adaptables en lugar de tablas anchas;
- confirmaciones explícitas para acciones financieras;
- documentos integrados dentro de la entidad que les da sentido;
- mensajes que orientan la recuperación ante errores.

La interfaz pública de acceso puede utilizarse para explicar el producto. Las pantallas autenticadas y sus datos reales se consideran confidenciales y no deben aparecer en portafolios, videos o demostraciones.

## Operación y entrega

1. Mantener actualizados arrendadores, arrendatarios y viviendas.
2. Confirmar el arrendador vigente antes de registrar un pago.
3. Registrar pagos únicamente después de comprobar el ingreso real.
4. Emitir y compartir el recibo desde el detalle del pago.
5. Anular en lugar de editar cuando una operación financiera deba invalidarse.
6. Eliminar documentos obsoletos desde la plataforma para retirar también el archivo físico.

Antes de publicar se ejecuta `npm run verify:release`. Después del despliegue se comprueban `/api/health`, captcha, inicio y cierre de sesión, rechazo de cuentas no autorizadas, permisos, documentos privados y los recorridos críticos de pagos y recibos.

La línea base de la versión 0.6.2 aprobó 41 pruebas automatizadas, compilación de producción y smoke test. Las pruebas automatizadas no sustituyen el recorrido manual con una cuenta real en móvil y escritorio.

## Fuera del alcance actual

- conciliación bancaria automática;
- cartera, saldos pendientes e intereses;
- envío automático por correo o WhatsApp;
- firma digital del comprobante;
- generación automática de contratos y coarrendatarios;
- aislamiento multiempresa, planes y facturación del servicio.

## Roadmap

### Prioridad alta

- generar contratos mediante plantillas y coarrendatarios;
- crear un ambiente demostrativo aislado;
- separar de forma segura datos, usuarios y permisos por organización.

### Prioridad media

- permitir marca configurable por empresa;
- incorporar importación masiva desde Excel;
- definir planes, facturación, soporte y condiciones;
- reforzar el portal público de verificación.

### Evolución posterior

- cartera, conciliación y automatizaciones financieras con alcance definido.

## Criterios antes de comercializar

- No utilizar datos reales de la operación familiar como demostración.
- Definir aislamiento por organización en base de datos, almacenamiento y autorización.
- Formalizar soporte, respaldo, privacidad, disponibilidad y responsabilidades.
- Separar marcas, configuraciones, consecutivos y usuarios por inmobiliaria.
- Diseñar una incorporación y capacitación de bajo esfuerzo.

## Aprendizajes

- Documentar primero el propósito y las fronteras de cada activo.
- Reutilizar patrones técnicos sin copiar entidades o procesos ajenos.
- Diferenciar información vigente de evidencia histórica.
- Modelar permisos según acciones reales, no solamente según pantallas.
- Diseñar la eliminación considerando también el almacenamiento físico.
- Tratar la interfaz como experiencia y no como frontera de seguridad.
- Incorporar pruebas de regresión para cada regla de negocio corregida en producción.
- Mantener los documentos dentro del proceso que les da contexto.

## Relación con otros documentos

- [Activos Digitales](../Activos_Digitales.md)
- [Soluciones Digitales](../capacidades/Soluciones_Digitales.md)
- [Método](../identidad/Metodo.md)
- [Revisión de Conocimiento](../conocimiento/Revision_de_Conocimiento.md)

## Historial de cambios

### 0.6.2

- Consolidación documental del activo en producción.
- Registro de alcance, arquitectura, seguridad, operación, límites y hoja de ruta.
