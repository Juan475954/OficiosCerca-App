# Catálogo de Funcionalidades y Flujos de Uso — OficiosCerca

Este documento detalla todas las funcionalidades implementadas en la aplicación móvil **OficiosCerca**, organizadas por módulos, perfiles de usuario (Cliente y Trabajador) y flujos de trabajo paso a paso.

---

## 1. Módulo de Autenticación y Registro

### 1.1 Inicio de Sesión (`LoginScreen`)
- **Autenticación por Credenciales:** Validación de formato de correo electrónico mediante expresiones regulares y contraseña mínima.
- **Conexión a Supabase Auth:** Comunicación asíncrona mediante GoTrue. Manejo amigable de errores para casos como credenciales inválidas, correo no confirmado, tiempo de espera de red o host no resuelto.
- **Carga Automática de Perfil:** Al autenticarse, consulta el perfil en la tabla `profiles` para determinar el rol del usuario (`CLIENT` o `WORKER`) y redirigirlo inmediatamente a su flujo correspondiente.
- **Detección y Cierre de Sesiones Inválidas:** Si una cuenta existe en Auth pero no posee un perfil o tiene un rol irreconocible, el sistema limpia la sesión local para evitar estados inconsistentes.
- **Recuperación de Contraseña:** Diálogo interactivo que solicita el correo electrónico del usuario y dispara el envío del correo de restablecimiento a través de Supabase Auth (`resetPasswordForEmail`).
- **Modo Demo / Visual:** Permite explorar la aplicación con datos simulados si Supabase no está configurado.

### 1.2 Registro de Usuario en Dos Pasos (`RegisterScreen` & `RoleSelectScreen`)
- **Paso 1: Datos Personales Básicos (`RegisterScreen`)**:
  - Campos: Nombre, Apellido, Correo electrónico, Teléfono celular, Ciudad/Ubicación, Contraseña y Confirmación de contraseña.
  - Validación local en tiempo real: campos no vacíos, formato de correo válido, coincidencia exacta de contraseñas y longitud mínima de 6 caracteres.
  - Almacenamiento temporal en `RegistrationDraft` dentro del ViewModel para no perder la información al navegar.
- **Paso 2: Selección de Rol y Oficio (`RoleSelectScreen`)**:
  - **Opción "Soy Cliente":** Crea el perfil con rol `client` y redirige directamente a la pantalla principal del cliente (`ClientHomeScreen`).
  - **Opción "Soy Trabajador":** Despliega el panel de selección de oficio principal con 6 especialidades oficiales (Plomería, Electricidad, Carpintería, Pintura, Cerrajería, Electrodomésticos). Al confirmar, crea el registro en `profiles`, genera el registro vinculado en `professionals` y asocia la categoría en `professional_services`.

---

## 2. Experiencia del Cliente (Módulo Cliente)

### 2.1 Pantalla Principal (`ClientHomeScreen`)
- **Banner de Estado de Conexión:** Indica en tiempo real si la aplicación está sincronizada con Supabase (mostrando el conteo de categorías y profesionales cargados) o si se encuentra en modo local/fallback.
- **Saludo Personalizado:** Muestra el nombre del cliente y su ubicación configurada.
- **Acceso Directo a Notificaciones:** Icono de campana con insignia numérica (badge) con el conteo de notificaciones no leídas.
- **Carrusel Horizontal de Categorías:** Botones interactivos con iconos dinámicos para filtrar el directorio por oficio en un solo toque.
- **Barra de Búsqueda Integrada:** Búsqueda en vivo por nombre de profesional, oficio o descripción del servicio.
- **Profesional Destacado:** Tarjeta con el profesional mejor calificado de la comunidad.
- **Listado de Profesionales Disponibles:** Tarjetas detalladas con foto/avatar, profesión, calificación en estrellas, años de experiencia, distancia y estado de disponibilidad.

### 2.2 Búsqueda y Resultados (`ClientSearchScreen` & `ClientResultsScreen`)
- **Buscador Avanzado:** Filtro simultáneo por categoría seleccionada y término de texto libre.
- **Sugerencias de Búsqueda:** Chips con palabras clave recomendadas (ej. "Reparación de fugas", "Instalación eléctrica", "Cambio de chapas").
- **Conteo Dinámico:** Muestra la cantidad exacta de profesionales que cumplen con el criterio de búsqueda.
- **Limpieza de Filtros:** Botón para reiniciar filtros y volver a ver el directorio completo.

### 2.3 Vista de Mapa (`ClientMapScreen`)
- **Mapa Interactivo Simulado:** Representación visual de la zona metropolitana de Medellín (Laureles, Belén, Poblado, Estadio, Robledo) con coordenadas relativas.
- **Pines de Geolocalización:** Marcadores visuales diferenciados para cada profesional según su disponibilidad.
- **Tarjeta Desplegable:** Al tocar un pin en el mapa, se despliega una tarjeta resumen del profesional con acceso directo a su perfil completo.

### 2.4 Detalle del Profesional (`ClientProfessionalDetailScreen`)
- **Encabezado y Reputación:** Nombre completo, oficio, calificación promedio, número total de servicios completados e insignia de disponibilidad ("Disponible ahora" vs "No disponible").
- **Biografía Profesional:** Resumen de trayectoria, años de experiencia y descripción del servicio.
- **Servicios Ofrecidos:** Lista de especialidades puntuales y precios/alcances referenciales.
- **Sección de Reseñas Reales:** Listado de opiniones dejadas por clientes anteriores con calificación en estrellas, fecha y comentario.
- **Botón de Acción Principal:** "Solicitar servicio", deshabilitado o con advertencia si el profesional no está disponible.

### 2.5 Flujo de Contratación en 3 Pasos
1. **Paso 1: Solicitud del Servicio (`ClientRequestServiceScreen`)**:
   - Selección de la categoría específica requerida.
   - Campo de texto obligatorio para describir detalladamente la necesidad o problema.
   - Selección de fecha deseada (ej. "Hoy", "Mañana" o fecha personalizada).
   - Selección de franja horaria preferida (Mañana / Tarde).
   - Dirección o ubicación de atención (precargada con la del perfil del cliente).
2. **Paso 2: Resumen y Confirmación (`ClientConfirmRequestScreen`)**:
   - Tarjeta resumen con los datos del profesional, categoría, descripción, dirección y horario estimado.
   - Advertencia de validación de datos antes del compromiso.
   - Botón "Confirmar y Enviar Solicitud".
3. **Paso 3: Confirmación de Envío (`ClientRequestSentScreen`)**:
   - Animación de éxito con verificación visual.
   - Opciones inmediatas: "Rastrear servicio" (lleva a la pantalla de seguimiento en vivo) o "Volver al inicio".

### 2.6 Seguimiento en Vivo (`ClientTrackingScreen`)
- **Barra de Progreso de 4 Fases:**
  1. *Solicitud enviada / Pendiente.*
  2. *Profesional asignado y en camino.*
  3. *Servicio en progreso en el domicilio.*
  4. *Servicio finalizado con éxito.*
- **Información del Profesional:** Acceso rápido a llamada telefónica directa (`tel:`) o mensajería.
- **Cancelación Segura:** Opción de cancelar la solicitud con confirmación visual si aún se encuentra en estado pendiente o en camino.
- **Botón para Calificar:** Se activa automáticamente en cuanto el servicio alcanza el estado `completed`.

### 2.7 Calificación y Reseña (`ClientRatingScreen`)
- **Selector de Estrellas Interactivo:** Permite elegir de 1 a 5 estrellas con retroalimentación visual dorada.
- **Comentario Opcional / Detallado:** Área de texto para describir la experiencia con el profesional.
- **Impacto Inmediato:** Al enviar la reseña, se inserta en Supabase (`reviews`) y se actualiza el promedio de calificación y servicios completados del trabajador.

### 2.8 Historial de Solicitudes (`ClientHistoryScreen`)
- **Listado Cronológico:** Visualización de todas las solicitudes realizadas por el cliente, ordenadas de la más reciente a la más antigua.
- **Pestañas de Filtrado:** Filtro rápido entre solicitudes "Activas" (pendientes, en camino, en proceso) y "Finalizadas/Canceladas".
- **Acceso a Detalle:** Cada tarjeta permite navegar directamente al seguimiento o a calificar si no ha sido evaluada.

### 2.9 Perfil del Cliente (`ClientProfileScreen`)
- **Visualización de Datos:** Nombre, correo, teléfono y dirección.
- **Edición en Modal:** Modificación en línea de nombre, teléfono y ubicación con actualización persistente en Supabase.
- **Cierre de Sesión:** Cierra la sesión en Supabase Auth y limpia el estado local del ViewModel.

---

## 3. Experiencia del Trabajador (Módulo Trabajador)

### 3.1 Panel de Control del Trabajador (`WorkerHomeScreen`)
- **Métricas Clave:**
  - Solicitudes pendientes de respuesta.
  - Trabajos activos en curso.
  - Trabajos finalizados acumulados.
  - Calificación promedio actual.
- **Interruptor de Disponibilidad (Toggle Switch):** Permite al trabajador marcarse como "Disponible para nuevos servicios" o "No disponible", reflejándose inmediatamente en el directorio de clientes.
- **Acceso Rápido a Solicitudes Nuevas:** Alertas visuales destacadas cuando ingresa una nueva petición de trabajo.

### 3.2 Gestión de Solicitudes Recibidas (`WorkerRequestsScreen` & `WorkerRequestDetailScreen`)
- **Bandeja de Entrada Filtrable:** Pestañas para organizar solicitudes "Pendientes", "En curso" y "Completadas".
- **Detalle de la Solicitud (`WorkerRequestDetailScreen`)**:
  - Datos del cliente: Nombre y número de teléfono con botón directo para llamada telefónica.
  - Ubicación del trabajo y horario propuesto.
  - Descripción exacta del daño o tarea.
  - **Acciones:** Botón de "Aceptar solicitud" y botón de "Rechazar solicitud".

### 3.3 Gestión del Trabajo Activo (`WorkerActiveServiceScreen`)
- **Avance de Estados Paso a Paso:**
  - El trabajador puede notificar al cliente que ha iniciado el traslado pulsando **"Voy en camino"** (`on_the_way`).
  - Al llegar al domicilio, cambia el estado a **"Iniciar trabajo"** (`in_progress`).
  - Al terminar las labores, confirma con **"Finalizar servicio"** (`completed`).
- **Llamada Rápida al Cliente:** Enlace nativo para comunicarse telefónicamente ante dudas de dirección o acceso.

### 3.4 Perfil del Profesional y Edición (`WorkerProfileScreen` & `WorkerEditProfileScreen`)
- **Detalle Público:** Cómo ven los clientes su perfil (oficio, experiencia, descripción, calificación).
- **Edición de Datos Profesionales (`WorkerEditProfileScreen`)**:
  - Cambio de oficio o profesión.
  - Actualización de años de experiencia verificable.
  - Redacción de la descripción y servicios incluidos.
  - Cambio de número de contacto y ubicación de cobertura.
- **Módulo de Reseñas (`WorkerReviewsScreen`)**: Listado completo de todas las opiniones y calificaciones recibidas por parte de sus clientes.

---

## 4. Módulo de Notificaciones (`ClientNotificationsScreen`)

- **Bandeja Unificada:** Lista de notificaciones recibidas por el usuario actual (cliente o trabajador).
- **Tipos de Notificaciones Soportados:**
  - Creación de solicitud (`"service_request"`).
  - Alerta de nuevo servicio para el trabajador (`"new_request"`).
  - Cambio de estado del servicio (`"status_update"`).
- **Gestión de Lectura:** Marcado automático o manual de notificaciones como leídas con persistencia en Supabase.
- **Refresco Manual:** Botón de actualización para sincronizar notificaciones recientes.

---

## 5. Matriz de Estado de Funcionalidades

| Funcionalidad | Módulo | Estado en el Proyecto |
| :--- | :--- | :---: |
| Autenticación por correo y contraseña | Auth | **Implementado** |
| Registro en dos fases con selección de rol | Auth | **Implementado** |
| Recuperación de contraseña vía email | Auth | **Implementado** |
| Autoreparación de perfiles (Self-Healing) | Auth / Repo | **Implementado** |
| Directorio de profesionales con búsqueda y categorías | Cliente | **Implementado** |
| Vista de mapa interactiva (simulada en Compose) | Cliente | **Implementado** |
| Solicitud de servicios en 3 pasos con validación | Cliente | **Implementado** |
| Seguimiento paso a paso del servicio | Cliente | **Implementado** |
| Sistema de calificaciones (1-5 estrellas) y reseñas | Cliente | **Implementado** |
| Historial de servicios con filtros activo/histórico | Cliente | **Implementado** |
| Panel de métricas para trabajadores | Trabajador | **Implementado** |
| Gestión de disponibilidad (Toggle Disponible) | Trabajador | **Implementado** |
| Aceptación y rechazo de solicitudes | Trabajador | **Implementado** |
| Avance de estados del servicio (`on_the_way` -> `completed`) | Trabajador | **Implementado** |
| Edición de perfil profesional y oficio | Trabajador | **Implementado** |
| Notificaciones en la aplicación | Notificaciones | **Implementado** |
| Fallback offline con datos mockeados | Resiliencia | **Implementado** |
| Integración con Google Maps SDK nativo | Cliente | *Pendiente / Futuro* |
| Notificaciones Push en segundo plano con Firebase FCM | General | *Pendiente / Futuro* |
| Chat de mensajería directa en tiempo real | Comunicación | *Pendiente / Futuro* |
| Pasarela de pagos electrónicos en la app | Pagos | *Pendiente / Futuro* |
| Carga de fotografías de evidencia a Supabase Storage | Multimedia | *Pendiente / Futuro* |

---

[← Volver al Modelo de Base de Datos](DATABASE.md) | [Ver Guía de Instalación →](INSTALLATION.md)
