# Guía de Contribución — OficiosCerca

¡Gracias por tu interés en colaborar con **OficiosCerca**! Este documento establece las directrices, estándares y flujos de trabajo recomendados para que nuevos desarrolladores y colaboradores puedan integrarse de manera fluida y mantener la calidad del proyecto.

---

## 1. Código de Conducta y Principios

Al participar en este proyecto, nos comprometemos a mantener un ambiente colaborativo, respetuoso, constructivo e inclusivo. Todas las interacciones en incidencias (issues), solicitudes de cambio (pull requests) y revisiones de código deben llevarse a cabo con profesionalismo técnico.

---

## 2. Preparación del Entorno

1. Realiza un **Fork** del repositorio a tu cuenta personal de GitHub.
2. Clona tu fork localmente:
   ```bash
   git clone https://github.com/<tu-usuario>/oficioscerca.git
   cd oficioscerca
   ```
3. Configura el repositorio principal como `upstream`:
   ```bash
   git remote add upstream https://github.com/<organizacion-o-propietario>/oficioscerca.git
   ```
4. Sigue los pasos de la [Guía de Instalación](INSTALLATION.md) para verificar que el proyecto compile y ejecute las pruebas locales sin errores:
   ```bash
   ./gradlew testDebugUnitTest
   ```

---

## 3. Estrategia de Ramas (Branching Model)

El proyecto utiliza un flujo de trabajo basado en ramas de características sobre `main` (o `develop` según la etapa del ciclo):

| Tipo de Rama | Patrón de Nombre | Propósito | Ejemplo |
| :--- | :--- | :--- | :--- |
| **Rama Principal** | `main` | Código de producción estable y entregable. | `main` |
| **Características** | `feature/<nombre-breve>` | Implementación de una nueva funcionalidad. | `feature/google-maps-integration` |
| **Corrección de Errores** | `fix/<nombre-breve>` | Resolución de un bug reportado. | `fix/tracking-status-advance` |
| **Documentación** | `docs/<tema>` | Creación o actualización de documentación. | `docs/api-guide` |
| **Refactorización** | `refactor/<modulo>` | Mejoras estructurales sin alterar funcionalidad. | `refactor/repository-cache` |

---

## 4. Convenciones de Commits (Conventional Commits)

Los mensajes de confirmación (commits) deben ser claros, en tiempo presente o infinitivo, y seguir el estándar de **Conventional Commits**:

```
<tipo>(<alcance_opcional>): <descripción breve y concisa>

[cuerpo explicativo opcional]

[referencias a issues, ej. Closes #12]
```

### Tipos admitidos:
- **`feat`**: Una nueva funcionalidad para el usuario.
  - *Ejemplo:* `feat(worker): agregar filtro por estado en bandeja de solicitudes`
- **`fix`**: Corrección de un error o bug.
  - *Ejemplo:* `fix(auth): corregir validacion de formato de correo electronico`
- **`docs`**: Cambios exclusivos en archivos de documentación.
  - *Ejemplo:* `docs(security): actualizar directrices de politicas RLS en Supabase`
- **`style`**: Ajustes de formato o estilos visuales que no modifican la lógica.
  - *Ejemplo:* `style(theme): estandarizar espaciado en tarjetas de servicio`
- **`refactor`**: Cambios de código que no corrigen errores ni añaden funcionalidades.
  - *Ejemplo:* `refactor(viewmodel): desacoplar lógica de filtros reactivos`
- **`test`**: Creación o modificación de pruebas unitarias o instrumentadas.
  - *Ejemplo:* `test(robolectric): agregar prueba de cancelacion de solicitud`
- **`chore`**: Tareas de mantenimiento, configuración de Gradle o metadatos de Git.
  - *Ejemplo:* `chore(deps): actualizar version de kotlin coroutines`

---

## 5. Estándares y Convenciones de Código Kotlin

1. **Guía de Estilo Oficial de Kotlin:**
   - Se debe seguir la guía oficial de Jetpack Compose y Kotlin (`kotlin.code.style=official`).
   - Sangría de 4 espacios (no usar tabs).
2. **Nombramiento en Jetpack Compose:**
   - Las funciones `@Composable` que retornan `Unit` y emiten elementos de UI deben usar **PascalCase** como sustantivos (ej. `ClientHomeScreen`, `OficiosButton`).
   - Las funciones que retornan valores normales deben usar **camelCase** (ej. `getCategoryIcon`).
3. **Flujo Unidireccional de Datos (UDF):**
   - No manipular variables globales ni invocar métodos suspendidos directamente en el cuerpo de una composable sin utilizar `LaunchedEffect`.
   - Delegar siempre los eventos al `ViewModel` mediante lambdas o llamadas directas.
4. **Inmutabilidad y StateFlow:**
   - Exponer siempre `StateFlow<T>` inmutables hacia la UI; mantener las variantes mutables `MutableStateFlow<T>` privadas dentro del `ViewModel` o `Repository`.
5. **No Almacenar Secretos en Código:**
   - Jamás incluir URLs privadas, claves de API, contraseñas o tokens en archivos `.kt` o `.xml`. Utilizar siempre `BuildConfig` inyectado mediante `.env`.

---

## 6. Proceso de Apertura de Pull Requests (PR)

1. **Actualiza tu rama con los cambios más recientes:**
   ```bash
   git checkout main
   git pull upstream main
   git checkout feature/mi-funcionalidad
   git rebase main
   ```
2. **Ejecuta las pruebas automatizadas:**
   ```bash
   ./gradlew testDebugUnitTest
   ```
3. **Envía tus cambios a tu fork:**
   ```bash
   git push origin feature/mi-funcionalidad
   ```
4. **Abre el Pull Request en GitHub:**
   - La plataforma cargará automáticamente la plantilla definida en `.github/PULL_REQUEST_TEMPLATE.md`.
   - Completa todos los campos requeridos: descripción del cambio, tipo de cambio, lista de verificación de pruebas y referencias a issues vinculados.
   - Si incluye cambios visuales en Compose, adjunta capturas de pantalla o un breve GIF del funcionamiento.
5. **Revisión de Código:**
   - Al menos un revisor del equipo debe aprobar el PR.
   - Resuelve constructivamente las observaciones o comentarios de revisión antes del merge.

---

[← Volver a Estructura del Proyecto](PROJECT_STRUCTURE.md) | [Ver Seguridad y Secretos →](SECURITY.md)
