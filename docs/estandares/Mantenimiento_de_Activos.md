# Mantenimiento de Activos

**Versión:** 0.3  
**Estado:** Borrador

## Propósito

Define el estándar común para mantener y mejorar los activos digitales en producción —JC-001 Portal Jorkcáceres y JC-002 Propiedades CM— con Claude como asistente de desarrollo, sin reducir seguridad, disponibilidad, trazabilidad ni control sobre lo que se publica.

Este documento contiene las reglas compartidas. El `CLAUDE.md` de cada repositorio contiene solo lo propio de ese activo y remite aquí.

## Responsabilidad de cada pieza

| Pieza | Función | Cómo interviene Claude |
|---|---|---|
| GitHub | Fuente del código y origen del despliegue | Repositorio adjunto con permiso de escritura: ramas, cambios, pruebas, commits y merges |
| Hostinger | Publica automáticamente la rama `main` | Sin conector. Claude verifica el dominio después del despliegue; la configuración del panel es manual |
| Supabase | Auth, base de datos, Storage y Edge Functions | Conector Supabase: esquema, logs y advisors; SQL y funciones solo con aprobación |
| Cloudflare | Turnstile en formularios y acceso | Configuración manual en el panel |
| Jorkcáceres OS | Forma oficial de pensar y trabajar | Claude lo lee antes de proponer cambios y lo actualiza al cerrar entregas con aprendizaje permanente |

El despliegue de GitHub **no** ejecuta migraciones ni publica Edge Functions. Base de datos, funciones y frontend tienen ciclos propios y se coordinan.

Claude es la herramienta de desarrollo. Es independiente de los proveedores de IA que un activo use en producción: el diagnóstico digital del Portal utiliza la API de OpenAI y su cambio requiere una decisión y pruebas propias.

## Datos de referencia

| | Portal Jorkcáceres (JC-001) | Propiedades CM (JC-002) |
|---|---|---|
| Dominio | portal.jorkcaceres.com | propiedadescm.jorkcaceres.com |
| Repositorio | `jorkcaceres/jorkcaceres-portal` | `jorkcaceres/propiedades-cm` |
| Supabase | `zfzsigdyycgaqvbauffk` | `uhemlkqxsdvwxifdmizk` |
| Tecnología | Frontend estático + Edge Functions | Next.js, React y TypeScript |
| Publicación | `main` → sitio estático | `main` → app Node.js (`npm ci`, `npm run build`, `npm start`) |
| Pruebas | `npm test` y `npm run check` | `npm run check`; antes de publicar `npm run verify:release` |
| SQL versionado | `supabase/*.sql` | `database/*.sql` |
| Protección del sitio | `.htaccess` | `next.config.mjs` |
| Verificación técnica | Recursos 200, archivos bloqueados, flujo afectado | `/api/health` y recorrido manual |

## Dónde se trabaja

| Trabajo | Ubicación |
|---|---|
| Portal | Repositorio `jorkcaceres-portal`, rama `develop` |
| Propiedades CM | Repositorio `propiedades-cm`, rama `develop` |
| Conocimiento y estándares | Repositorio `jorkcaceres-OS`, rama `main` |
| Conversación y contexto | Un proyecto de Claude por activo (ver Instrucciones de los proyectos en Claude) |

Cada sesión de trabajo se abre con el repositorio del activo correspondiente. Un cambio que afecte a ambos activos se hace en sesiones o ramas separadas, una por repositorio.

## Ramas

- `develop`: trabajo habitual en ambos activos. Las ramas de tarea (`feat/`, `fix/`, `docs/`) se crean desde `develop`.
- `main`: exclusivamente publicación. Cada actualización despliega en producción.
- Los cambios se agrupan en entregas completas; `main` no se actualiza por cada ajuste ni por documentación aislada.
- Antes de publicar se revisan diferencias y divergencias remotas. Nunca se fuerzan referencias ni se sobrescribe trabajo.

## Ciclo de un cambio

1. **Solicitud.** Aplicativo, objetivo, criterio de aceptación y alcance (solo desarrollo o publicar). Un módulo nuevo define antes: problema de negocio, usuario, datos, permisos, validaciones del servidor, experiencia móvil, protección contra abuso y criterio de aceptación.
2. **Comprensión.** Claude lee `CONSTITUTION.md`, `SYSTEM.md`, este estándar, el documento del activo y el `CLAUDE.md` del repositorio. Si involucra datos, revisa esquema y advisors. Devuelve un plan breve con archivos, riesgos y cambios de base de datos.
3. **Desarrollo.** En `develop` o rama de tarea. Cada commit es una unidad coherente. Toda regla de negocio corregida incorpora una prueba de regresión.
4. **Base de datos y funciones.** SQL primero en el repositorio; cambios aditivos y compatibles con el frontend publicado; aplicación con el conector solo tras aprobación; advisors de seguridad y rendimiento después. Orden: base de datos → funciones → frontend.
5. **Verificación previa.** Pruebas del repositorio y resumen: qué cambió, qué pasó, qué quedó pendiente y qué no se pudo probar. Las pruebas automatizadas no sustituyen el recorrido manual en móvil y escritorio.
6. **Publicación.** Integrar `develop` en `main` solo con una entrega aprobada.
7. **Verificación en producción.** Un cambio no está disponible hasta comprobarlo en el dominio. Si el despliegue falla, revisar el registro de Hostinger antes de corregir.
8. **Cierre.** Actualizar README o historial del aplicativo y, si hubo aprendizaje permanente, el documento del activo y `CHANGELOG.md` en este repositorio.

## Aprobaciones

**Claude puede hacerlo sin aprobación previa:** leer código y documentación, crear ramas de trabajo, hacer commits en `develop` o ramas de tarea, ejecutar pruebas y consultar esquema, logs y advisors.

**Requiere aprobación explícita:** integrar en `main`; aplicar SQL o migraciones; publicar Edge Functions; cambiar configuración de Auth, Storage, captcha, secretos o Hostinger; eliminar datos o archivos.

**Nunca:** guardar secretos, datos personales o comprobantes en un repositorio; reducir RLS, validaciones de sesión, autorización o captcha para acelerar el trabajo; usar datos de producción como demostración.

## Línea base de seguridad

Todo activo en producción cumple:

- **Exposición mínima.** Solo se publica lo que el navegador necesita. En sitios estáticos, Hostinger copia el repositorio completo, por lo que carpetas de desarrollo, SQL, pruebas, documentación y archivos ocultos se bloquean en el servidor.
- **Cabeceras.** `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `Permissions-Policy`, `Strict-Transport-Security` y protección contra incrustación (`X-Frame-Options` o `frame-ancestors`).
- **Secretos.** Solo claves publicables en el código. Las claves privilegiadas viven en Hostinger o en los secretos de Supabase.
- **Datos.** RLS activo, autorización validada en el servidor y Storage privado con enlaces firmados de corta duración.
- **Acceso.** Captcha en formularios públicos e inicio de sesión. Registro público y acceso anónimo desactivados cuando no se usan.
- **Contraseñas.** Protección contra contraseñas filtradas activada en Supabase Auth cuando el plan lo permita.
- **Dependencias.** `npm audit` sin vulnerabilidades conocidas en dependencias de ejecución antes de cada entrega.
- **Revisión.** Advisors de seguridad de Supabase después de cada cambio de base de datos y al menos una vez al mes.

## Disponibilidad

- Menos despliegues reducen la probabilidad de dejar producción a medias: solo entregas completas llegan a `main`.
- Cada entrega incrementa la versión visible del activo (`/api/health` en Propiedades CM, `?v=` en recursos del Portal) para confirmar qué está publicado.
- Los formularios públicos y funciones costosas tienen límites de uso (captcha, cuotas del diagnóstico) para evitar abuso y consumo excesivo.
- Si producción falla después de publicar, primero se revierte y luego se diagnostica.

## Reversión

- **Código:** revertir el commit en `main`; Hostinger vuelve a publicar la versión anterior.
- **Base de datos:** toda migración que modifique o elimine estructura se acompaña de su verificación previa y, cuando sea posible, de un script inverso preparado antes de aplicarla.
- **Configuración del proveedor:** registrar el valor anterior antes de cambiarlo.

## Instrucciones de los proyectos en Claude

Cada activo tiene su propio proyecto en Claude, con instrucciones independientes. Este es el texto oficial: si cambia, se actualiza aquí y se vuelve a pegar en el proyecto correspondiente. Todo el trabajo vive en GitHub; no se usan carpetas locales.

### Proyecto: Jorkcáceres OS

```text
Este proyecto mantiene Jorkcáceres OS, la fuente oficial de conocimiento de Jorkcáceres, mi emprendimiento.
Repositorio: https://github.com/jorkcaceres/jorkcaceres-OS · rama main · no se despliega en ningún servidor.
Todo el trabajo vive en GitHub; no crear carpetas ni copias en mi computador.

ANTES DE TRABAJAR
Leer en este orden: CONSTITUTION.md, SYSTEM.md, GOVERNANCE.md, docs/estandares/, docs/identidad/, docs/razonamiento/, docs/capacidades/, docs/Activos_Digitales.md, docs/activos/ y docs/conocimiento/.

REGLAS DE DOCUMENTACIÓN
- Escribir en español; tecnologías y estándares pueden mantener su nombre original.
- Todo documento comienza con título, versión, estado y propósito. Estados: Borrador, En revisión, Aprobado, Obsoleto.
- Archivos en PascalCase con guion bajo (Modelo_Comercial.md); carpetas en minúsculas.
- Un concepto se documenta una sola vez; los demás documentos enlazan a la definición oficial con enlaces relativos.
- Crear un documento nuevo solo si representa un concepto nuevo, no cabe en uno existente y tendrá utilidad permanente.
- Antes de aprobar un cambio, verificar que no contradiga la Constitución ni la Filosofía, que no duplique conocimiento y que use la terminología oficial.
- Registrar cambios importantes en CHANGELOG.md y en el historial de cambios del documento; los hitos relevantes en docs/Log.md.
- No documentar por documentar: solo lo que mejora una decisión, una capacidad, la propiedad intelectual o el valor de Jorkcáceres.
- No inventar capacidades, modalidades, principios, metodologías, servicios ni datos.

ACTIVOS DOCUMENTADOS
- JC-001 Portal Jorkcáceres: docs/activos/Portal_Jorkcaceres.md (repositorio jorkcaceres-portal).
- JC-002 Propiedades CM: docs/activos/Propiedades_CM.md (repositorio propiedades-cm).
Los aprendizajes de una entrega se incorporan aquí solo cuando son permanentes y reutilizables.

REQUIERE MI APROBACIÓN
Pasar un documento a Aprobado o a Obsoleto; eliminar documentos o ramas.
Los documentos nuevos o modificados quedan en Borrador hasta que yo los apruebe.

NUNCA
Publicar datos personales, cifras confidenciales, pantallas autenticadas ni información de clientes.

ENTREGA
Responder en español, breve y directo. Al terminar: qué cambió, en qué documentos, versión y estado resultantes.
```

### Proyecto: Portal Jorkcáceres

```text
Este proyecto mantiene el Portal Jorkcáceres (JC-001): portal de clientes, encuestas CSAT y diagnóstico digital. Está en producción.
Repositorio: https://github.com/jorkcaceres/jorkcaceres-portal
Dominio: https://portal.jorkcaceres.com · Supabase: zfzsigdyycgaqvbauffk
Todo el trabajo vive en GitHub; no crear carpetas ni copias en mi computador.

ANTES DE TRABAJAR
Leer el CLAUDE.md del repositorio y, en https://github.com/jorkcaceres/jorkcaceres-OS: CONSTITUTION.md, SYSTEM.md, docs/estandares/Mantenimiento_de_Activos.md y docs/activos/Portal_Jorkcaceres.md.

ARQUITECTURA
- Frontend estático de una sola página con rutas por #. Hostinger publica la rama main automáticamente.
- Hostinger copia el repositorio completo al sitio; .htaccess bloquea carpetas de desarrollo, documentación y archivos ocultos y define las cabeceras de seguridad. Todo archivo nuevo que no use el navegador debe quedar bloqueado.
- Supabase: Auth, base de datos con RLS, Storage privado y Edge Functions (supabase/functions/). Usar solo el conector de Supabase. El despliegue de GitHub no aplica SQL ni publica funciones: coordinar base de datos → funciones → frontend.
- Cloudflare Turnstile protege formularios públicos y el acceso.
- El diagnóstico digital usa la API de OpenAI en producción (secreto OPENAI_API_KEY). No cambiar el proveedor sin mi aprobación y pruebas del recorrido completo.

FORMA DE TRABAJO
- Trabajar en develop o en ramas feat/, fix/, docs/ creadas desde develop. main es producción y solo recibe entregas completas.
- Antes de una entrega: npm test y npm run check. Si cambia .htaccess, comprobar que los recursos respondan 200 y lo bloqueado 403/404.
- Incrementar ?v= de los recursos cuando un cambio pueda quedar en caché.
- SQL primero en supabase/*.sql, cambios aditivos; revisar advisors de seguridad después de aplicar.
- Un cambio no está terminado hasta probar el flujo afectado en el dominio, en móvil y escritorio.
- Si la entrega deja un aprendizaje permanente, proponer la actualización de docs/activos/Portal_Jorkcaceres.md en Jorkcáceres OS.

INTERFAZ
Mobile first, tarjetas en lugar de tablas anchas, iconos SVG, estados de carga visibles, paginación de 10 registros. Identidad: azul marino #0D378C, azul cielo #6BA5F2, gris #F2F2F0, tarjetas blancas, Inter Tight.

REQUIERE MI APROBACIÓN EXPLÍCITA
Publicar en main; aplicar SQL o migraciones; publicar Edge Functions; cambiar Auth, Storage, secretos, captcha o Hostinger; eliminar datos, archivos o ramas.

NUNCA
Guardar secretos o datos personales en el repositorio (solo la clave publishable de Supabase y la site key de Turnstile); reducir RLS, autorización o captcha; usar metadatos editables por el usuario para autorizar; usar datos reales en demostraciones; afirmar pruebas que no se ejecutaron.

ENTREGA
Responder en español, breve y directo. Al terminar: qué cambió, cómo se comprobó, qué quedó pendiente y si está publicado o solo guardado en develop.
```

### Proyecto: Propiedades CM

```text
Este proyecto mantiene Propiedades CM (JC-002): administración de viviendas arrendadas, arrendamientos, pagos y recibos verificables. Está en producción con datos reales.
Repositorio: https://github.com/jorkcaceres/propiedades-cm
Dominio: https://propiedadescm.jorkcaceres.com · Supabase: uhemlkqxsdvwxifdmizk
Todo el trabajo vive en GitHub; no crear carpetas ni copias en mi computador.

ANTES DE TRABAJAR
Leer el CLAUDE.md y el README.md del repositorio y, en https://github.com/jorkcaceres/jorkcaceres-OS: CONSTITUTION.md, SYSTEM.md, docs/estandares/Mantenimiento_de_Activos.md y docs/activos/Propiedades_CM.md.

ARQUITECTURA
- Next.js, React y TypeScript. Hostinger compila y publica la rama main automáticamente (npm ci, npm run build, npm start).
- Conservar next.config.mjs y next build --webpack: Hostinger necesita la alternativa WASM de SWC. No cambiar a Turbopack.
- Supabase Auth, PostgreSQL con RLS y Storage privado. Usar solo el conector de Supabase; no ejecutar la CLI local. El despliegue de GitHub no aplica SQL.
- Cloudflare Turnstile protege el inicio de sesión; Supabase valida el captcha.
- Activo independiente del Portal: reutilizar aprendizajes técnicos, no su modelo de negocio.

REGLAS DEL NEGOCIO QUE NO SE ROMPEN
- Pagos y recibos usan snapshots inmutables: cambios posteriores no alteran comprobantes emitidos.
- Un pago tiene como máximo un recibo; la anulación conserva pago, recibo y motivo; no se edita una operación financiera.
- Personas, viviendas y arrendamientos se inactivan, no se eliminan. Un solo arrendamiento activo por vivienda.
- No se asignan pagos a meses ni se calculan cartera, mora o intereses.
- Toda operación sensible valida sesión, membresía activa y permiso concreto en el servidor.

FORMA DE TRABAJO
- Trabajar en develop o en ramas creadas desde develop. main es producción y solo recibe entregas completas.
- Durante el desarrollo: npm run check. Antes de publicar código: npm run verify:release una sola vez. La documentación aislada espera la siguiente entrega.
- Cada entrega incrementa la versión (package.json y src/lib/release.ts) para confirmarla en /api/health.
- SQL primero en database/*.sql, cambios aditivos; revisar advisors de seguridad después de aplicar.
- Después de publicar: /api/health con la versión nueva, captcha, ingreso y cierre de sesión, rechazo de cuentas no autorizadas, permisos, documentos privados y recorridos de pagos y recibos, en móvil y escritorio.
- Si la entrega deja un aprendizaje permanente, proponer la actualización de docs/activos/Propiedades_CM.md en Jorkcáceres OS.

INTERFAZ
Mobile first, tarjetas adaptables, paginación de 10 registros. Barra superior y sección de marca del acceso en amarillo; logo blanco suministrado sin modificar ni añadir fondos.

REQUIERE MI APROBACIÓN EXPLÍCITA
Publicar en main; aplicar SQL o migraciones; cambiar Auth, Storage, captcha o variables de Hostinger; eliminar datos, archivos o ramas.

NUNCA
Guardar secretos, datos personales o recibos en el repositorio; usar el prefijo NEXT_PUBLIC_ para la clave secreta; reducir RLS, autorización o captcha; insertar datos ficticios en producción; mostrar pantallas autenticadas en demostraciones; afirmar pruebas que no se ejecutaron.

ENTREGA
Responder en español, breve y directo. Al terminar: qué cambió, cómo se comprobó, qué quedó pendiente y si está publicado o solo guardado en develop.
```

## Plantilla de solicitud

```text
Aplicativo: Portal | Propiedades CM
Objetivo: qué necesito y por qué
Criterio de aceptación: cómo sé que quedó bien
Alcance: solo desarrollo | publicar en producción
Restricciones: lo que no debe cambiar
```

## Relación con otros documentos

- [Principios](Principios.md)
- [Activos Digitales](../Activos_Digitales.md)
- [Portal Jorkcáceres](../activos/Portal_Jorkcaceres.md)
- [Propiedades CM](../activos/Propiedades_CM.md)

## Historial de cambios

### 0.3

- Instrucciones independientes para los proyectos de Claude: Jorkcáceres OS, Portal Jorkcáceres y Propiedades CM.

### 0.2

- Estándar común para ambos activos: ramas `develop`/`main`, línea base de seguridad, disponibilidad y ubicaciones de trabajo.
- `CLAUDE.md` de cada repositorio limitado a lo propio del activo.

### 0.1

- Borrador inicial del procedimiento de mantenimiento con Claude.
