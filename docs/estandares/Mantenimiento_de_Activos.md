# Mantenimiento de Activos

**Versión:** 0.2  
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
| Conversación y contexto | Proyecto «Mi vida profesional» en Claude |

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

### 0.2

- Estándar común para ambos activos: ramas `develop`/`main`, línea base de seguridad, disponibilidad y ubicaciones de trabajo.
- `CLAUDE.md` de cada repositorio limitado a lo propio del activo.

### 0.1

- Borrador inicial del procedimiento de mantenimiento con Claude.
