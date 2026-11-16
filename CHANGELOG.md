# Registro de Cambios (Changelog) — OficiosCerca

Todos los cambios notables realizados en este proyecto serán documentados en este archivo.

El formato está basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/), y este proyecto se adhiere a [Semantic Versioning](https://semver.org/lang/es/).

---

## [1.0.0] - Versión Inicial Documentada

### Añadido
- **Arquitectura y Core:**
  - Arquitectura orientada a componentes con **MVVM (Model-View-ViewModel)** y patrón **Repository**.
  - Interfaz de usuario 100% declarativa construida con **Jetpack Compose** y Material 3.
  - Navegación reactiva basada en estados mediante jerarquía sellada `Screen` y pila de navegación (`screenStack`) con soporte para botón Atrás del sistema.
  - Catálogo centralizado de dependencias mediante Gradle Version Catalog (`gradle/libs.versions.toml`).
- **Autenticación y Perfiles:**
  - Integración con **Supabase Auth (GoTrue)** para registro, inicio de sesión y recuperación de contraseña por correo electrónico.
  - Registro en dos fases con selección de roles de usuario (`CLIENT` y `WORKER`).
  - Panel de selección de oficio inicial para trabajadores entre 6 especialidades oficiales (Plomería, Electricidad, Carpintería, Pintura, Cerrajería, Electrodomésticos).
  - Mecanismo de autoreparación de perfiles (`self-healing`) para restaurar registros en `profiles` a partir de metadatos de sesión.
  - Edición en vivo de perfiles de cliente y trabajador.
- **Módulo de Cliente:**
  - Pantalla principal (`ClientHomeScreen`) con carrusel de categorías de oficios, trabajador destacado y directorio de profesionales cercanos en Medellín.
  - Buscador reactivo de trabajadores con sugerencias de palabras clave y filtros por oficio.
  - Vista de mapa interactiva en Jetpack Compose con pines geolocalizados y tarjetas emergentes de profesionales.
  - Pantalla de detalle profesional con biografía, lista de servicios estructurados, reputación y reseñas de clientes.
  - Flujo de solicitud de servicios en 3 pasos: formulario de necesidad y horario, pantalla de confirmación previa y confirmación animada post-envío.
  - Pantalla de seguimiento en tiempo real (`ClientTrackingScreen`) con indicador visual de 4 fases y opción de cancelación.
  - Módulo de calificación y reseñas con selector interactivo de estrellas (1 a 5) y comentario textual.
  - Historial de solicitudes del cliente con pestañas de filtrado (activas vs finalizadas).
- **Módulo de Trabajador:**
  - Panel de control (`WorkerHomeScreen`) con tarjetas de métricas (solicitudes pendientes, en curso, finalizadas y promedio de calificación).
  - Interruptor de disponibilidad (toggle) para activar o pausar la recepción de nuevos trabajos.
  - Bandeja de solicitudes de trabajo con opciones para aceptar o rechazar solicitudes.
  - Pantalla de gestión de servicio activo (`WorkerActiveServiceScreen`) para avanzar secuencialmente el estado del servicio (`on_the_way` -> `in_progress` -> `completed`).
  - Módulo de edición de perfil profesional y consulta de reseñas recibidas.
- **Notificaciones y Comunicación:**
  - Centro de notificaciones en la aplicación con insignia de mensajes no leídos y marcado individual como leída.
  - Enlaces nativos para comunicación telefónica directa (`Intent.ACTION_DIAL`).
- **Resiliencia y Modo Offline:**
  - Fuente de datos simulada en memoria (`MockData`) con fallback automático cuando no hay conexión con Supabase o no existen variables de entorno configuradas.
- **Documentación y Preparación para GitHub:**
  - Documentación técnica exhaustiva en `docs/` (`ARCHITECTURE.md`, `DATABASE.md`, `FUNCTIONALITIES.md`, `INSTALLATION.md`, `PROJECT_STRUCTURE.md`, `CONTRIBUTING.md`, `SECURITY.md`).
  - Archivos maestros de configuración de Git: `.gitignore`, `.gitattributes`, `CONTRIBUTING.md` y `LICENSE` preliminar.
  - Plantillas de incidencias y pull requests en `.github/`.
