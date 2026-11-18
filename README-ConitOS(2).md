# ConitOS Launcher v1.2 — APK compilado y listo para instalar

ConitOS es un launcher HOME para TV Box Android con estética glassmorphism
(vidrio, transparencias, esquinas muy redondeadas), botón de voz protagonista,
reloj + clima y accesos rápidos totalmente editables.

- **Paquete:** `com.conitos.launcher`
- **Versión:** 1.2 (versionCode 3)
- **Compatible:** Android 7.0 (API 24) o superior — cubre la gran mayoría de los TV Box en circulación
- **Firma:** APK firmado (esquemas v2/v3) con la keystore incluida `conitos.jks`
- **Actualización:** misma firma desde la v1.0 → se instala encima con `adb install -r`, sin desinstalar nada (se conservan fondo, ciudad, accesos y ajustes)

---

## Novedades de la v1.2

1. **Fondos animados: GIF y video MP4 ya se reproducen.**
   - **GIF animado**: en Ajustes → "Elegir imagen o GIF". Se reproduce en bucle
     con recorte tipo "centerCrop" (cubre toda la pantalla). Sin dependencias
     externas (usa el decodificador GIF del SDK).
   - **Video MP4**: botón nuevo **"Elegir video (MP4)"**. Se reproduce en bucle,
     **siempre mudo** (es el fondo del launcher), con recorte centerCrop, y se
     reinicia solo al volver al inicio. Soporta MP4/H.264 (el estándar de los
     convertidores de video); si el formato no lo decodifica el TV Box, avisa
     con un mensaje.
   - Solo uno activo a la vez: al elegir GIF o video se reemplaza al fondo
     anterior (imagen, GIF o video). "Quitar fondo" borra cualquiera.
   - Los GIF se guardan sin recomprimir (se conserva la animación completa) y
     el video se copia en segundo plano con tope de 300 MB.
2. **Texto de versión eliminado.** Abajo a la izquierda ya no aparece
   "ConitOS 1.x · ciudad". La barra inferior solo tiene el botón de ajustes,
   alineado a la derecha.

## Novedades de la v1.1

1. **Foco de los accesos rápidos corregido.** El círculo ya no se recorta al
   agrandarse (`clipChildren=false` en toda la jerarquía).
2. **TTS reparado**: reintentos de motor (Google TTS / Pico / eSpeak), cadena
   de idiomas `es-AR → es-MX → es-US → es-ES → es-419 → es → sistema`,
   mensajes pendientes hasta que el motor esté listo y salida por stream con
   volumen si la música está en 0.
3. **Voz de mujer o de hombre** (Ajustes → Voz del asistente) con muestra
   audible al cambiar y botón "Probar voz".

---

## 1. Contenido del paquete

| Archivo | Descripción |
|---|---|
| `ConitOS-Launcher-v1.2.apk` | APK compilado y firmado, listo para instalar |
| `ConitOS-fuente-v1.2.zip` | Código fuente completo + script de compilación + keystore |
| `README-ConitOS.md` | Este documento |

## 2. Instalación

### Opción A — Por ADB (recomendado)
```bash
adb install -r ConitOS-Launcher-v1.2.apk
```
Activa "Depuración por USB" o "Depuración por red" en el TV Box.

### Opción B — Desde el propio TV Box
1. Copiá el APK a un pendrive (formateado en FAT32/exFAT).
2. Abrilo con el explorador de archivos del TV Box (o una app como "File Manager").
3. Aceptá "Instalar" y habilitá "Orígenes desconocidos" si lo pide.

## 3. Arranque automático (launcher predeterminado)

ConitOS se declara como **aplicación HOME** (con soporte Leanback para TV).
Para que el TV Box arranque directo en ConitOS al encender:

1. Instalá el APK.
2. Presioná el **botón HOME del control remoto**.
3. Elegí **"ConitOS"** y marcá **"Siempre"**.

En la mayoría de los equipos esto hace que ConitOS se abra automáticamente al
encender (el launcher HOME de Android se inicia al completar el boot). Si el
firmware tiene un launcher propio muy clavado, podés desactivarlo con ADB:

```bash
# Ver el launcher actual
adb shell cmd package resolve-activity --brief -c android.intent.category.HOME | tail -1

# Deshabilitarlo (reemplazá com.fabricante.launcher por el que devuelva el comando anterior)
adb shell pm disable-user --user 0 com.fabricante.launcher
```

Para revertir: `adb shell pm enable com.fabricante.launcher`.

## 4. Botón de micrófono del control remoto

- El botón con micrófono (o el de búsqueda) del control **abre directamente la
  interfaz de escucha** sobre el propio launcher (panel translúcido flotante,
  sin salir de la app).
- También hay un **botón grande de voz al centro del Home**.
- Motor de voz: usa el `SpeechRecognizer` de Android. Si el TV Box no tiene
  motor de voz instalado (pasa en boxes genéricos), el launcher lo detecta y
  ofrece un **campo de texto para comandos** como alternativa.
  Para voz completa, instalá la app de Google (Google Search/Assistant) desde
  Play Store; para voz 100% offline se puede integrar Vosk en una versión futura.

## 5. Comandos de voz soportados (español rioplatense, sin IA)

| Decí | Hace |
|---|---|
| "Abrí YouTube" / "Poné Netflix" / "Abrí Kodi" / "Abrí IPTV" / "Abrí Chrome" / "Abrí Play Store" | Abre la app (busca también cualquier app instalada por nombre, ej: "Abrí Spotify") |
| "Abrí Configuración" / "Abrí Ajustes" | Abre los ajustes de Android |
| "Subí el volumen" / "Bajá el volumen" | Ajusta el volumen con UI del sistema |
| "Silencio" / "Mute" | Alterna el silencio |
| "Subí el brillo" / "Bajá el brillo" | Ajusta el brillo (pide permiso especial la primera vez) |
| "¿Qué hora es?" | Responde en pantalla y por voz |
| "¿Cómo está el clima?" / "Temperatura" | Responde con el clima configurado |
| "Reiniciá" / "Reiniciá el equipo" | Reinicia (requiere permisos de sistema, ver §8) |
| "Apagá el equipo" | Apaga (requiere permisos de sistema, ver §8) |

## 6. Clima

- Servicio **Open-Meteo** (gratis, sin API key, ideal para Salta y todo el mundo).
- Ciudad por defecto: **Salta Capital (-24.78, -65.41)**.
- Configurable en **Ajustes → Reloj y clima**: escribir ciudad, "Guardar ciudad"
  (geocodificación automática), "Detectar ubicación" (por IP) o "Actualizar clima".
- Muestra temperatura, humedad y viento, con icono según condición (sol, luna,
  nubes, lluvia, nieve, tormenta, niebla) y cambio día/noche automático.

## 7. Fondo y personalización (Ajustes en la esquina inferior derecha)

- **Fondo: imagen, GIF animado o video MP4** (nuevo en v1.2): en bucle, mudo,
  cubre la pantalla con recorte centerCrop. "Quitar fondo" borra cualquiera.
- Fondo imagen: **desenfoque automático** (GPU en Android 12+, software en
  versiones anteriores), intensidad configurable. (El GIF/video se muestran
  sin desenfoque para mantener la fluidez.)
- Vidrio: transparencia y color (Blanco, Grafito, Azul, Violeta, Menta).
- Color de acento Material You (incluye modo **Auto** que toma el color del
  fondo de pantalla en Android 8.1+).
- Reloj: tamaño (56–140sp), segundos on/off, modo claro/oscuro automático por hora.
- Accesos rápidos: hasta 10 íconos, cambiar/quitar/agregar, tamaño de íconos.
- Voz del asistente: Automática / Mujer / Hombre + botón "Probar voz" +
  estado del motor TTS.
- Animaciones on/off, respuesta por voz (TTS) on/off.

## 8. Reiniciar y apagar por voz

`PowerManager.reboot()` es un permiso de sistema: una app instalada
normalmente no puede apagar el equipo. Opciones:

1. **Usar el comando físico** del control (mantener botón de encendido).
2. **Instalar ConitOS como app de sistema** (requiere root o firmware accesible):
   ```bash
   adb root && adb remount
   adb push ConitOS-Launcher-v1.2.apk /system/priv-app/ConitOS.apk
   adb reboot
   ```
   Instalada en `/system/priv-app`, el launcher gana los permisos necesarios
   y "Reiniciá" / "Apagá el equipo" pasan a funcionar por completo.

## 9. Stack técnico

Este build fue compilado **directamente con el Android SDK** (aapt2 + d8 +
apksigner) para obtener un APK liviano (~670 KB), sin dependencias externas:

| Pedido en el diseño | Implementación en v1.2 |
|---|---|
| Interfaz moderna | Android SDK nativo (Java), sin dependencias; listo para migrar a Kotlin + Jetpack Compose |
| Material You | Acento dinámico desde el fondo de pantalla (WallpaperColors) + presets |
| SpeechRecognizer offline | SpeechRecognizer de Android con fallback de texto; Vosk puede integrarse en v2 |
| TTS con voz de mujer/hombre | Clase `Tts` (singleton): motores alternativos, cadena de idiomas es-*, voces por género + tono |
| Fondo GIF | `GifBackgroundView` (Movie del SDK, en bucle, centerCrop) |
| Fondo video | `VideoBackgroundView` (MediaPlayer + TextureView, bucle, mudo, centerCrop) |
| Weather API | Open-Meteo (sin clave) |
| Room | SharedPreferences + JSON (mismo objetivo, menor peso) |
| Coil | BitmapFactory con muestreo + caché simple |
| Lottie | Animaciones nativas (ObjectAnimator/ValueAnimator) |
| Google Sans | Tipografía del sistema (sans-serif); "Google Sans" es propietaria de Google y no se puede redistribuir |

El código fuente está organizado en una estructura estándar de proyecto Android
(manifest, res/, src/) e incluye `build_apk.sh` para recompilar sin Gradle, o
puede importarse en Android Studio para migrar a Kotlin/Compose.

## 10. Estructura del proyecto

```
conitos/
├── AndroidManifest.xml          HOME + LEANBACK_LAUNCHER + permisos
├── conitos.jks                  keystore de firma (pass: conitos2026)
├── res/
│   ├── layout/                  activity_main.xml, activity_settings.xml
│   ├── drawable/                iconos Material Symbols (vectores), fondos
│   ├── drawable-nodpi/          intro_logo.png (tu logo "ConitOS" metálico)
│   ├── mipmap-*/                icono de la app
│   └── values/                  colores, tema, strings
└── src/com/conitos/launcher/
    ├── MainActivity.java        Home: intro, reloj, clima, vidrio, voz, accesos, fondos
    ├── SettingsActivity.java    Personalización completa + voz mujer/hombre
    ├── Tts.java                 Síntesis de voz robusta (v1.1)
    ├── GifBackgroundView.java   Fondo GIF animado en bucle (v1.2)
    ├── VideoBackgroundView.java Fondo video MP4 en bucle y mudo (v1.2)
    ├── CommandParser.java       Parser de comandos de voz (sin IA)
    ├── WeatherService.java      Open-Meteo + geocodificación
    ├── AppRepository.java       Listado de apps + búsqueda difusa
    ├── BlurUtil.java            RenderEffect (GPU) / box blur (CPU)
    ├── Prefs.java               Preferencias
    ├── UiUtil.java              Glassmorphism, acento, utilidades
    └── BootReceiver.java        Precarga del clima al encender
```

## 11. Recompilar

Requiere JDK 17 y Android SDK (build-tools 34, platform 34):

```bash
bash build_apk.sh
```

El script hace todo: aapt2 → javac → d8 → zipalign → apksigner y deja el APK
firmado como `ConitOS-Launcher-v1.2.apk`.

## 12. Solución de problemas

| Problema | Solución |
|---|---|
| El GIF no se anima | Asegurate de elegirlo con "Elegir imagen o GIF" (no con el botón de video). GIF muy grandes pueden tardar 1-2 s en arrancar. Si el GIF es muy raro, se muestra su primer frame como imagen fija. |
| El video no se reproduce | Usá MP4 (H.264 + AAC), el formato estándar. Si el box no decodifica el archivo, aparece un aviso. Convertí con HandBrake o similar a "MP4 1080p Fast". |
| El video se escucha | Es intencional: el fondo siempre está mudo. |
| El asistente no habla nada | Ajustes → Voz → mirá el estado del motor debajo de "Probar voz". Si dice "no disponible", instalá "Síntesis de voz de Google" desde Play Store y elegilo en Ajustes de Android → Salida de texto a voz. |
| La voz de mujer/hombre suena igual | El motor de la box solo tiene una voz: ConitOS ajusta el tono automáticamente. Con Google TTS instalado se usan voces reales de mujer/hombre. |
| "No hay motor de voz" (para escuchar) | Instalá la app de Google / Google Search desde Play Store, o usá el campo de texto del panel de voz |
| El clima no carga | Verificá Internet; en Ajustes usá "Actualizar clima" |
| El desenfoque se ve menos suave | En Android < 12 se usa desenfoque por software; podés ajustar la intensidad en Ajustes → Desenfoque |
| No arranca al encender | Presioná HOME y elegí "ConitOS · Siempre"; si el firmware lo impide, deshabilitá el launcher del fabricante con ADB (§3) |
| "Reiniciá/Apagá" no funciona | Es normal en apps normales: requiere instalación como app de sistema (§8) |
| Desinstalar | `adb uninstall com.conitos.launcher` |
