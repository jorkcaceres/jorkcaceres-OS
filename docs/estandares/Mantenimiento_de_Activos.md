# Mantenimiento de Activos con IA

**Versión:** 0.1  
**Estado:** Borrador

## Propósito

Define el procedimiento para mantener y mejorar los activos digitales en producción (JC-001 Portal Jorkcáceres y JC-002 Propiedades CM) con Claude como asistente de desarrollo, sin reducir seguridad, trazabilidad ni control sobre lo que se publica.

## Responsabilidad de cada pieza

| Pieza | Función | Cómo interviene Claude |
|---|---|---|
| GitHub | Fuente del código y origen del despliegue | Sesión de código con el repositorio adjunto: ramas, cambios, pruebas, commits y merges |
| Hostinger | Publica automáticamente la rama `main` | Sin conector. Claude verifica el dominio después del despliegue; la configuración del panel es manual |
| Supabase | Auth, base de datos, Storage y Edge Functions | Conector Supabase: lectura de esquema, logs y advisors; migraciones y funciones solo con aprobación |
| Cloudflare | Turnstile en formularios y acceso | Configuración manual en el panel; el conector actual no administra Turnstile |
| Jorkcáceres OS | Forma oficial de pensar y trabajar | Claude lo lee antes de proponer cambios y lo actualiza al cerrar una entrega con aprendizaje permanente |

El despliegue de GitHub **no** ejecuta migraciones ni publica Edge Functions. Base de datos, funciones y frontend tienen ciclos propios y deben coordinarse.

En el Portal, Hostinger copia el repositorio completo al sitio: todo archivo en `main` queda accesible públicamente en el dominio.

Claude es la herramienta de desarrollo. Es independiente de los proveedores de IA que un activo use en producción: el diagnóstico digital del Portal utiliza la API de OpenAI y su cambio requiere una decisión y pruebas propias.

## Datos de referencia

| | Portal Jorkcáceres (JC-001) | Propiedades CM (JC-002) |
|---|---|---|
| Dominio | portal.jorkcaceres.com | propiedadescm.jorkcaceres.com |
| Repositorio | `jorkcaceres/jorkcaceres-portal` | `jorkcaceres/propiedades-cm` |
| Supabase | `zfzsigdyycgaqvbauffk` | `uhemlkqxsdvwxifdmizk` |
| Tecnología | Frontend estático + Edge Functions | Next.js 16, React 19, TypeScript |
| Publicación | `main` → `public_html/portal` | `main` → app Node.js (`npm ci`, `npm run build`, `npm start`) |
| Rama de trabajo | Rama de tarea (`feat/`, `fix/`, `docs/`) | `develop` o rama de tarea derivada |
| Pruebas | `npm test` y `npm run check` | `npm run check`; antes de publicar `npm run verify:release` |
| SQL versionado | `supabase/*.sql` | `database/*.sql` |
| Reglas del repositorio | `CLAUDE.md` | `CLAUDE.md` |
| Verificación técnica | Flujo afectado en el dominio | `/api/health` y recorrido manual |

## Preparación única

1. **GitHub:** instalar la aplicación de Claude en GitHub con acceso solo a los tres repositorios (`jorkcaceres-OS`, `jorkcaceres-portal`, `propiedades-cm`). Sin este paso Claude puede leer, pero no guardar cambios.
2. **Reglas en cada repositorio:** cada aplicativo mantiene un `CLAUDE.md` en la raíz con sus reglas de trabajo. Claude lo lee al iniciar cada sesión.
3. **Supabase:** mantener el conector autorizado. En Propiedades CM, Supabase se administra únicamente mediante el conector; no se usa la CLI local.
4. **Hostinger:** confirmar en el panel que cada sitio sigue conectado a `main` y que las variables de entorno de Propiedades CM están completas.
5. **Proyecto en Claude:** trabajar desde el proyecto «Mi vida profesional», con instrucciones que remitan a Jorkcáceres OS como fuente oficial.

## Ciclo de un cambio

### 1. Solicitud

Indicar aplicativo, problema u objetivo y criterio de aceptación. Si el cambio crea un módulo nuevo, definir antes: problema de negocio, usuario, datos, permisos, validaciones del servidor, experiencia móvil, protección contra abuso y criterio de aceptación.

### 2. Comprensión

Claude lee `SYSTEM.md`, `CONSTITUTION.md`, el documento del activo en `docs/activos/` y las reglas del repositorio. Revisa el código afectado y, si involucra datos, el esquema y los advisors de Supabase. Devuelve un plan breve con archivos, riesgos y si requiere cambios en base de datos.

### 3. Desarrollo

- Propiedades CM: trabajar en `develop` o en una rama derivada.
- Portal: trabajar en una rama de tarea creada desde `main`.
- Cada commit representa una unidad coherente de cambio.
- Toda regla de negocio corregida incorpora una prueba de regresión.
- Los cambios estáticos relevantes del Portal incrementan la versión de los recursos para evitar problemas de caché.

### 4. Base de datos y funciones

1. El SQL se guarda primero en el repositorio (`database/` o `supabase/`).
2. Se prefieren cambios aditivos y compatibles con la versión publicada del frontend.
3. Claude presenta el SQL y su efecto; se aplica con el conector solo después de la aprobación.
4. Después de aplicar, Claude revisa los advisors de seguridad y rendimiento.
5. Las Edge Functions del Portal se publican con el conector, también con aprobación previa.

Orden habitual: base de datos compatible → funciones → frontend.

### 5. Verificación previa

Claude ejecuta las pruebas del repositorio y presenta un resumen: qué cambió, qué pruebas pasaron, qué queda pendiente y qué no pudo probar. Las pruebas automatizadas no sustituyen el recorrido manual en móvil y escritorio.

### 6. Publicación

Se integra en `main` solo cuando se aprueba una entrega completa. En Propiedades CM, los cambios se agrupan en `develop` y se integran en `main` una sola vez por entrega; la documentación aislada espera la siguiente entrega. Nunca se fuerzan referencias ni se sobrescribe trabajo remoto.

### 7. Verificación en producción

Un cambio en `main` no se considera disponible hasta verificar producción.

- Propiedades CM: `/api/health` muestra la versión esperada; captcha, ingreso, cierre de sesión, rechazo de cuentas no autorizadas, permisos, documentos privados y recorridos de pagos y recibos.
- Portal: flujo modificado, visibilidad del cliente, permisos administrativos y, si aplica, recorrido completo del diagnóstico.

Si falla, revisar el registro de compilación de Hostinger antes de corregir.

### 8. Cierre

- Actualizar `README.md` o historial del aplicativo cuando cambie el alcance.
- Si la entrega produjo un aprendizaje permanente, actualizar el documento del activo en Jorkcáceres OS y `CHANGELOG.md`.

## Qué hace Claude por su cuenta y qué requiere aprobación

**Sin aprobación previa:** leer código y documentación, crear ramas de trabajo, hacer commits en ramas de trabajo o `develop`, ejecutar pruebas, consultar esquema, logs y advisors.

**Con aprobación explícita:** integrar en `main`, aplicar SQL o migraciones en producción, publicar Edge Functions, cambiar configuración de Auth o Storage, eliminar datos o archivos, y cualquier cambio que afecte secretos.

**Nunca:** guardar secretos, datos personales o comprobantes en el repositorio; reducir RLS, validaciones de sesión, autorización o captcha para acelerar el trabajo; usar datos de producción como demostración.

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

- [Activos Digitales](../Activos_Digitales.md)
- [Portal Jorkcáceres](../activos/Portal_Jorkcaceres.md)
- [Propiedades CM](../activos/Propiedades_CM.md)
- [Principios](Principios.md)

## Historial de cambios

### 0.1

- Borrador inicial del procedimiento de mantenimiento con Claude.
