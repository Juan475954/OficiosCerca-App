# Guía de Instalación y Ejecución — OficiosCerca

Esta guía proporciona instrucciones detalladas paso a paso para configurar el entorno de desarrollo, sincronizar dependencias, configurar el backend y compilar/ejecutar la aplicación móvil **OficiosCerca**.

---

## 1. Requisitos Previos del Sistema

Antes de comenzar, asegúrate de contar con los siguientes componentes en tu estación de trabajo:

| Componente | Versión Requerida | Notas |
| :--- | :--- | :--- |
| **Sistema Operativo** | Windows 10/11 (64-bit), macOS (12+) o Linux (Ubuntu 22.04+) | Compatible con entornos de virtualización (VT-x / AMD-V habilitado en BIOS para emulador). |
| **Android Studio** | **Android Studio Ladybug (2024.2.1)** o superior | Se recomienda la versión más reciente compatible con el Android Gradle Plugin 9.x. |
| **Java Development Kit (JDK)** | **JDK 17** o **JDK 21** | Gradle 9.3.1 requiere un JDK moderno. Android Studio incluye Eclipse Temurin / JetBrains Runtime por defecto. |
| **Android SDK** | **Android SDK 36** (con herramientas para API 24 a 36) | `minSdk = 24` (Android 7.0), `targetSdk = 36`, `compileSdk = 36`. |
| **Git** | 2.30 o superior | Para clonación y control de versiones. |
| **Memoria RAM** | Mínimo 8 GB (Recomendado 16 GB o más) | Gradle daemon y el emulador de Android consumen ~4 a 6 GB durante compilación. |

---

## 2. Clonación del Repositorio

Abre una terminal o consola Git Bash y ejecuta:

```bash
git clone https://github.com/<tu-organizacion-o-usuario>/oficioscerca.git
cd oficioscerca
```

> [!NOTE]
> Si descargaste el proyecto como archivo ZIP, descomprímelo en un directorio de tu preferencia (asegúrate de que la ruta no contenga espacios ni caracteres especiales).

---

## 3. Configuración de Variables de Entorno (`.env`)

El proyecto utiliza el plugin **Secrets Gradle Plugin** para inyectar credenciales de forma segura durante la compilación.

1. Localiza el archivo plantilla `.env.example` en la raíz del proyecto.
2. Crea una copia de este archivo y nómbralo exactamente `.env`:
   - En Windows (PowerShell):
     ```powershell
     Copy-Item .env.example .env
     ```
   - En Linux / macOS:
     ```bash
     cp .env.example .env
     ```
3. Abre el archivo `.env` en un editor de texto y define tus credenciales de Supabase:

```properties
# GEMINI_API_KEY: Opcional si utilizas funciones generativas
GEMINI_API_KEY=tu_clave_gemini_aqui

# SUPABASE: Requerido para sincronización en la nube con Supabase
SUPABASE_URL=https://tu-proyecto.supabase.co
SUPABASE_PUBLISHABLE_KEY=tu_anon_publishable_key_de_supabase
```

> [!TIP]
> **¿No tienes un proyecto de Supabase configurado aún?**
> No te preocupes: OficiosCerca incluye un **modo offline/demo automático**. Si dejas los valores por defecto (`YOUR_SUPABASE_URL`), la aplicación se compilará y ejecutará sin problemas utilizando los datos de prueba (`MockData`).

---

## 4. Apertura y Sincronización en Android Studio

1. Abre **Android Studio**.
2. En la pantalla de bienvenida, selecciona **Open** (o ve a `File > Open...`).
3. Navega hasta la carpeta del proyecto y selecciona el directorio que contiene los archivos `build.gradle.kts` y `settings.gradle.kts`.
4. Espera a que Android Studio detecte los archivos Gradle e inicie la descarga inicial de paquetes.
5. Si no se inicia automáticamente, haz clic en el botón de sincronización de Gradle: **Sync Project with Gradle Files** (icono con el elefante azul en la barra de herramientas superior).

### Verificación de la versión de JDK en Android Studio:
1. Ve a `File > Settings` (en Windows/Linux) o `Android Studio > Settings` (en macOS).
2. Navega a `Build, Execution, Deployment > Build Tools > Gradle`.
3. En **Gradle JDK**, asegúrate de que esté seleccionado un JDK 17 o JDK 21 (ej. *Embedded JDK* o *jbr-17*).

---

## 5. Ejecución en Emulador de Android (AVD)

1. En Android Studio, abre el gestor de dispositivos: **Device Manager** (`Tools > Device Manager`).
2. Haz clic en **Create Virtual Device**.
3. Selecciona un dispositivo de referencia (por ejemplo: **Pixel 7** o **Pixel 8**).
4. Elige una imagen de sistema:
   - **Recomendado:** Android 14 (API 34, `UpsideDownCake`) o Android 15 (API 35/36).
   - Tipo de imagen: **Google Play Intel x86_64 Atom System Image** (o **Google APIs ARM64** para equipos Mac con procesador Apple Silicon M1/M2/M3/M4).
5. Asigna al menos 2048 MB de RAM al AVD en la configuración avanzada.
6. Inicia el emulador haciendo clic en el botón **Play (▶)**.
7. Una vez que el emulador esté encendido, selecciona el dispositivo en la barra superior de Android Studio y presiona **Run 'app' (Shift + F10)**.

---

## 6. Ejecución en Dispositivo Físico Android

1. En tu teléfono Android, ve a `Ajustes > Acerca del teléfono`.
2. Presiona 7 veces consecutivas sobre **Número de compilación** para desbloquear las *Opciones de desarrollador*.
3. Ve a `Ajustes > Sistema > Opciones de desarrollador` y activa la casilla **Depuración por USB (USB Debugging)**.
4. Conecta tu teléfono a la computadora mediante un cable USB de buena calidad.
5. En la pantalla del teléfono, acepta el mensaje de autorización de la huella digital RSA de la computadora marcando "Permitir siempre desde este equipo".
6. En Android Studio, verifica que tu teléfono aparezca en el menú desplegable de dispositivos.
7. Presiona **Run 'app'**.

---

## 7. Compilación desde la Línea de Comandos (CLI)

Si prefieres compilar sin abrir Android Studio, puedes utilizar el Gradle Wrapper incluido:

### 7.1 Compilar APK de Depuración (Debug APK)
- **En Windows (PowerShell o CMD):**
  ```powershell
  .\gradlew assembleDebug
  ```
- **En Linux / macOS:**
  ```bash
  chmod +x ./gradlew
  ./gradlew assembleDebug
  ```

El archivo APK resultante se generará en la ruta:
```
app/build/outputs/apk/debug/app-debug.apk
```

### 7.2 Instalar directamente en un dispositivo conectado por ADB:
```bash
./gradlew installDebug
```

### 7.3 Ejecutar las Pruebas Unitarias y de Robolectric:
```bash
./gradlew testDebugUnitTest
```

---

## 8. Solución de Problemas Frecuentes

### Error: "Could not find google-services.json"
- **Causa:** El plugin de Firebase espera el archivo de configuración.
- **Solución:** Ya está resuelto en `gradle.properties` mediante `googleServices.missing.passthrough=true` y en `app/build.gradle.kts` con estrategia `WARN`. No impedirá la compilación.

### Error: "Could not connect to Kotlin compile daemon"
- **Solución:** Ya está mitigado en `gradle.properties` configurando `kotlin.compiler.execution.strategy=in-process`. Si persiste, detén los daemons de Gradle con `./gradlew --stop` y reintenta.

### Error: "Out of Memory / Java heap space"
- **Solución:** Revisa la línea `org.gradle.jvmargs=-Xmx4g` en `gradle.properties`. Si tu computadora cuenta con 8 GB o menos de RAM, puedes ajustar temporalmente el valor a `-Xmx2g`.

---

[← Volver a Funcionalidades](FUNCTIONALITIES.md) | [Ver Estructura del Proyecto →](PROJECT_STRUCTURE.md)
