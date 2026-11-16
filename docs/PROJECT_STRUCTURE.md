# Estructura del Proyecto — OficiosCerca

Este documento describe la estructura de directorios, la organización de paquetes y la responsabilidad de cada archivo clave en el repositorio de **OficiosCerca**.

---

## 1. Árbol General del Directorio

```
oficioscerca/
├── .github/                           # Metadatos, plantillas de issues y pull requests para GitHub
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md             # Plantilla estandarizada para reportar errores
│   │   └── feature_request.md        # Plantilla para proponer nuevas funcionalidades
│   └── PULL_REQUEST_TEMPLATE.md      # Plantilla para revisión y apertura de Pull Requests
├── app/                              # Módulo principal de la aplicación Android
│   ├── src/
│   │   ├── main/                     # Código fuente de producción y recursos
│   │   │   ├── java/com/example/     # Código fuente Kotlin (lógica, UI, servicios)
│   │   │   │   ├── data/             # Fuentes de datos locales y datos de prueba
│   │   │   │   ├── model/            # Modelos de dominio y DTOs serializables
│   │   │   │   ├── service/          # Clientes de red, autenticación y repositorios
│   │   │   │   ├── ui/               # Interfaces declarativas en Jetpack Compose
│   │   │   │   │   ├── auth/         # Pantallas de autenticación y registro
│   │   │   │   │   ├── client/       # Pantallas de la experiencia del cliente
│   │   │   │   │   ├── components/   # Componentes y widgets reutilizables
│   │   │   │   │   ├── theme/        # Tema, tipografía y paleta cromática oficial
│   │   │   │   │   └── worker/       # Pantallas del panel del trabajador
│   │   │   │   ├── viewmodel/        # ViewModels y definición de rutas de navegación
│   │   │   │   └── MainActivity.kt   # Actividad principal (Single Activity)
│   │   │   ├── res/                  # Recursos Android (layouts, drawables, strings, temas)
│   │   │   └── AndroidManifest.xml   # Manifiesto de Android con permisos y actividades
│   │   ├── test/                     # Pruebas unitarias locales y con Robolectric
│   │   └── androidTest/              # Pruebas instrumentadas para dispositivo/emulador
│   ├── .gitignore                    # Reglas de exclusión locales para el módulo app
│   ├── build.gradle.kts              # Script de compilación y dependencias del módulo app
│   └── proguard-rules.pro            # Reglas de ofuscación y optimización ProGuard/R8
├── docs/                             # Documentación técnica completa y profesional
│   ├── ARCHITECTURE.md               # Arquitectura MVVM, UDF y diagramas técnicos
│   ├── DATABASE.md                   # Esquema relacional de Supabase / PostgreSQL
│   ├── FUNCTIONALITIES.md            # Catálogo de funcionalidades y casos de uso
│   ├── INSTALLATION.md               # Guía paso a paso de instalación y compilación
│   ├── PROJECT_STRUCTURE.md          # Estructura del proyecto y responsabilidades (este archivo)
│   ├── CONTRIBUTING.md               # Guía de contribución, flujo Git y código de conducta
│   └── SECURITY.md                   # Políticas de seguridad, RLS y gestión de secretos
├── gradle/
│   ├── libs.versions.toml            # Catálogo centralizado de versiones y dependencias (Version Catalog)
│   └── wrapper/
│       └── gradle-wrapper.properties # Configuración de versión del Gradle Wrapper (9.3.1)
├── .env.example                      # Plantilla de variables de entorno (Supabase / Gemini)
├── .gitattributes                    # Configuración de normalización de fin de línea en Git
├── .gitignore                        # Reglas maestras de exclusión para Git en Android
├── build.gradle.kts                  # Script de configuración raíz del proyecto Gradle
├── CHANGELOG.md                      # Historial de cambios y versiones del proyecto
├── CONTRIBUTING.md                   # Enlace raíz a la guía de contribución
├── gradle.properties                 # Propiedades de compilación y memoria de la JVM
├── LICENSE                           # Archivo de licencia (propuesta pendiente de ratificación)
├── metadata.json                     # Metadatos del proyecto y capacidades declaradas
├── README.md                         # Portada principal y presentación técnica del repositorio
└── settings.gradle.kts               # Configuración de repositorios y módulos incluidos
```

---

## 2. Responsabilidades por Capa y Módulo

### 2.1 Paquete Raíz (`com.example`)

- **`MainActivity.kt`**:
  - Hereda de `ComponentActivity`.
  - Configura el soporte Edge-to-Edge (`enableEdgeToEdge()`).
  - Aloja la función composable raíz `OficiosApp(viewModel)`.
  - Maneja la navegación del botón físico o gesto Atrás del sistema mediante `BackHandler`.
  - Despliega la barra de navegación inferior (`ClientBottomNavBar`) de forma condicional para las pantallas del cliente.

---

### 2.2 Paquete de Modelos (`com.example.model`)

- **`Models.kt`**:
  - **Enums del Sistema:**
    - `UserRole`: `CLIENT`, `WORKER`.
    - `ServiceStatus`: `PENDING`, `ACCEPTED`, `ON_THE_WAY`, `IN_PROGRESS`, `COMPLETED`, `CANCELLED` (con método de validación de transiciones `canTransitionTo()` y parseo tolerante `fromCode()`).
  - **DTOs de Supabase (anotados con `@Serializable` de Kotlinx):**
    - `SupabaseProfileDto`: Mapeo de la tabla `profiles` con propiedades computadas como `displayName`.
    - `SupabaseCategoryDto`: Mapeo de la tabla `categories`.
    - `SupabaseProfessionalDto`: Mapeo de la tabla `professionals` con relaciones embebidas.
    - `SupabaseProfessionalInsertDto`: Payload estructurado para inserción de nuevos trabajadores.
    - `SupabaseProfessionalServiceDto`: Mapeo de la tabla intermedia `professional_services`.
    - `SupabaseServiceRequestDto`: Mapeo de la tabla `service_requests`.
    - `SupabaseReviewDto`: Mapeo de la tabla `reviews`.
    - `SupabaseNotificationDto`: Mapeo de la tabla `notifications`.
  - **Modelos de Dominio Limpios:**
    - `UserProfile`, `ServiceCategory`, `Professional`, `ProfessionalServiceItem`, `ServiceRequest`, `Review`, `AppNotification`.

---

### 2.3 Paquete de Servicios y Datos (`com.example.service` y `com.example.data`)

- **`SupabaseClient.kt`**:
  - Singleton de conexión con Supabase.
  - Normaliza la URL configurada en `BuildConfig.SUPABASE_URL` para remover barras finales o subrutas no deseadas.
  - Expone el indicador booleano `isConfigured` para verificar si las credenciales son válidas o se debe operar en modo demo.
  - Inicializa bajo demanda (`lazy`) los módulos `Auth`, `Postgrest`, `Storage` y `Realtime`.
- **`AuthService.kt`**:
  - Wrapper alrededor del módulo GoTrue de Supabase.
  - Métodos asíncronos para `signIn`, `signUp` (con inyección de metadatos de nombre, teléfono y rol), `resetPassword`, `signOut` (con estrategia de degradación a cierre local si falla el cierre global) y consulta de usuario/sesión activa.
- **`OficiosRepository.kt`**:
  - Define la interfaz `OficiosRepository` con los contratos de consulta y mutación.
  - Provee la clase `InMemoryOficiosRepository`, la cual orquesta la sincronización con Supabase PostgREST y ofrece fallback automático a `MockData` en caso de desconexión o falta de credenciales.
  - Aloja la lógica de autoreparación de perfiles (`self-healing`) y el cálculo en memoria de promedios de calificación.
- **`MockData.kt`**:
  - Provee conjuntos iniciales de datos realistas para Medellín (barrios Laureles, Belén, Poblado, Estadio, Robledo) con profesionales, categorías, solicitudes previas y notificaciones de prueba.

---

### 2.4 Paquete de ViewModel (`com.example.viewmodel`)

- **`OficiosViewModel.kt`**:
  - Contiene la clase sellada `Screen` que define todas las rutas de navegación válidas.
  - Define las interfaces selladas de estado de UI: `ProfessionalDetailUiState`, `CreateRequestUiState`, `ClientRequestsUiState`, `WorkerRequestsUiState`, `WorkerActionUiState`, `ProfileActionUiState`, `ReviewActionUiState`, `ClientActionUiState`.
  - Gestiona la pila de navegación `screenStack`.
  - Expone los flujos de datos (`currentUser`, `categories`, `professionals`, `filteredProfessionals`, `requests`, `notifications`, etc.).
  - Procesa eventos de usuario: inicio de sesión, registro, envío de solicitudes, avance de estados de servicio, calificaciones y actualización de perfiles.

---

### 2.5 Paquete de Interfaz de Usuario (`com.example.ui`)

- **`ui/auth/AuthScreens.kt`**:
  - Pantallas de autenticación: `WelcomeScreen`, `LoginScreen`, `RegisterScreen`, `RoleSelectScreen`.
  - Incluye la lista de oficios oficiales con sus UUIDs de Supabase (`officialTrades`).
- **`ui/client/`**:
  - `ClientBottomNavBar.kt`: Barra de navegación inferior con 4 pestañas (Inicio, Buscar, Mis solicitudes, Perfil).
  - `ClientHomeScreen.kt`: Pantalla principal con carrusel de categorías, destacado y profesionales disponibles.
  - `ClientSearchAndResults.kt`: Pantalla de búsqueda avanzada y vista de resultados filtrados.
  - `ClientMapScreen.kt`: Vista de mapa con pines interactivos.
  - `ClientDetailAndRequest.kt`: Detalle completo del profesional y formulario de solicitud de servicio.
  - `ClientConfirmAndSent.kt`: Pantalla de confirmación previa y pantalla de confirmación animada post-envío.
  - `ClientTrackingAndRating.kt`: Seguimiento paso a paso del servicio y formulario de calificación con estrellas.
  - `ClientHistoryAndProfile.kt`: Historial de solicitudes del cliente, perfil de usuario y centro de notificaciones.
- **`ui/worker/`**:
  - `WorkerHomeAndRequests.kt`: Dashboard con métricas del trabajador, toggle de disponibilidad y lista de solicitudes entrantes.
  - `WorkerDetailActiveAndProfile.kt`: Detalle de solicitud recibida, gestión del servicio activo (avance de estados), edición de perfil profesional y visualización de reseñas.
- **`ui/components/CommonComponents.kt`**:
  - Botones institucionales (`OficiosButton`, `OficiosOutlinedButton`).
  - Avatares con iniciales o imágenes (`UserAvatar`).
  - Barra de estrellas interactivas y fijas (`RatingStars`).
  - Insignia de estado con código de color semántico (`StatusBadge`).
  - Vistas de estado vacío (`EmptyStateView`).
- **`ui/theme/`**:
  - `Color.kt`: Paleta de colores oficial (#071952, #088395, #37B7C3, #EBF4F6, #FFFFFF) y colores semánticos de estado.
  - `Theme.kt`: Configuración del `MaterialTheme` de Material 3 para Compose.
  - `Type.kt`: Configuración de tipografía e interlineados.

---

### 2.6 Pruebas Automatizadas (`app/src/test/`)

- **`OficiosCercaRobolectricTest.kt`**:
  - Batería de pruebas unitarias y de integración de UI ejecutadas sobre la JVM usando **Robolectric** (`@Config(sdk = [34])`).
  - Valida el flujo completo de navegación, filtrado reactivo en ViewModel, selección de roles, envío de solicitudes y avance de estados.
- **`ExampleUnitTest.kt` & `ExampleRobolectricTest.kt`**:
  - Pruebas base de verificación del entorno.

---

[← Volver a Instalación](INSTALLATION.md) | [Ver Guía de Contribución →](CONTRIBUTING.md)
