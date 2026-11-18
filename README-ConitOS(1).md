# ConitOS Launcher v1.1 — APK compilado y listo para instalar

ConitOS es un launcher HOME para TV Box Android con estética glassmorphism
(vidrio, transparencias, esquinas muy redondeadas), botón de voz protagonista,
reloj + clima y accesos rápidos totalmente editables.

- **Paquete:** `com.conitos.launcher`
- **Versión:** 1.1 (versionCode 2)
- **Compatible:** Android 7.0 (API 24) o superior — cubre la gran mayoría de los TV Box en circulación
- **Firma:** APK firmado (esquemas v2/v3) con la keystore incluida `conitos.jks`
- **Actualización:** misma firma que la v1.0 → se instala encima con `adb install -r`, sin desinstalar nada (se conservan fondo, ciudad y accesos configurados)

---

## Novedades de la v1.1

1. **Foco de los accesos rápidos corregido.** Al navegar con el control, el
   círculo se agrandaba y Android lo recortaba a su tamaño original (se veía
   cortado / deformado). Ahora toda la jerarquía de contenedores tiene
   `clipChildren=false`: el círculo crece suave por fuera de sus límites y se
   ve completo, con su borde de acento. El anillo de pulso del micrófono
   también dejó de recortarse.
2. **TTS reparado (ya responde con voz).** Antes el motor se configuraba con
   `es-AR` a ciegas y sin reintentos: si la TV Box no tenía español argentino
   (o no tenía motor de voz configurado), quedaba mudo. Ahora:
   - Si el motor del sistema falla, prueba automáticamente con **Google TTS,
     Pico y eSpeak** (el que esté instalado).
   - Idioma con cadena de reemplazo: `es-AR → es-MX → es-US → es-ES → es-419 → es → idioma del sistema`.
   - Si pedís hablar antes de que el motor termine de inicializar, el texto
     queda **pendiente** y se dice apenas esté listo (antes se perdía).
   - Si la salida multimedia está en volumen 0 pero el sistema no, envía el
     audio por el stream del sistema (situación típica en boxes genéricas).
3. **Voz de mujer o de hombre (estilo Loquendo).** En **Ajustes → Voz y
   animaciones → Voz del asistente** podés elegir **Automática / Mujer /
   Hombre**. Al cambiar se escucha una muestra al instante y hay un botón
   **"Probar voz"**. Funciona en dos niveles:
   - Si el motor tiene voces con género (Google TTS y otros), elige la voz
     real de mujer o de hombre en español.
   - Si el motor solo trae una voz, ajusta el tono (agudo / grave) para que
     la diferencia se escuche igual.
   - Ajustes muestra el estado real del motor (ej: "Motor: Google TTS ·
     Voz: es-AR (mujer)") para poder diagnosticar en el propio TV Box.

---

## 1. Contenido del paquete

| Archivo | Descripción |
|---|---|
| `ConitOS-Launcher-v1.1.apk` | APK compilado y firmado, listo para instalar |
| `ConitOS-fuente-v1.1.zip` | Código fuente completo + script de compilación + keystore |
| `README-ConitOS.md` | Este documento |

## 2. Instalación

### Opción A — Por ADB (recomendado)
```bash
adb install -r ConitOS-Launcher-v1.1.apk
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

## 7. Personalización (Ajustes en la esquina inferior derecha)

- Fondo: imagen personalizada + **desenfoque automático** (GPU en Android 12+,
  software en versiones anteriores), intensidad configurable.
- Vidrio: transparencia y color (Blanco, Grafito, Azul, Violeta, Menta).
- Color de acento Material You (incluye modo **Auto** que toma el color del
  fondo de pantalla en Android 8.1+).
- Reloj: tamaño (56–140sp), segundos on/off, modo claro/oscuro automático por hora.
- Accesos rápidos: hasta 10 íconos, cambiar/quitar/agregar, tamaño de íconos.
- **Voz del asistente: Automática / Mujer / Hombre** + botón "Probar voz" +
  estado del motor TTS (nuevo en v1.1).
- Animaciones on/off, respuesta por voz (TTS) on/off.

## 8. Reiniciar y apagar por voz

`PowerManager.reboot()` es un permiso de sistema: una app instalada
normalmente no puede apagar el equipo. Opciones:

1. **Usar el comando físico** del control (mantener botón de encendido).
2. **Instalar ConitOS como app de sistema** (requiere root o firmware accesible):
   ```bash
   adb root && adb remount
   adb push ConitOS-Launcher-v1.1.apk /system/priv-app/ConitOS.apk
   adb reboot
   ```
   Instalada en `/system/priv-app`, el launcher gana los permisos necesarios
   y "Reiniciá" / "Apagá el equipo" pasan a funcionar por completo.

## 9. Stack técnico

Este build fue compilado **directamente con el Android SDK** (aapt2 + d8 +
apksigner) para obtener un APK liviano (~670 KB), sin dependencias externas:

| Pedido en el diseño | Implementación en v1.1 |
|---|---|
| Interfaz moderna | Android SDK nativo (Java), sin dependencias; listo para migrar a Kotlin + Jetpack Compose |
| Material You | Acento dinámico desde el fondo de pantalla (WallpaperColors) + presets |
| SpeechRecognizer offline | SpeechRecognizer de Android con fallback de texto; Vosk puede integrarse en v2 |
| TTS con voz de mujer/hombre | Clase `Tts` (singleton): motores alternativos, cadena de idiomas es-*, voces por género + tono |
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
    ├── MainActivity.java        Home: intro, reloj, clima, vidrio, voz, accesos
    ├── SettingsActivity.java    Personalización completa + voz mujer/hombre
    ├── Tts.java                 Síntesis de voz robusta (nuevo en v1.1)
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
firmado como `ConitOS-Launcher-v1.1.apk`.

## 12. Solución de problemas

| Problema | Solución |
|---|---|
| El asistente no habla nada | Ajustes → Voz → mirá el estado del motor debajo de "Probar voz". Si dice "no disponible", instalá "Síntesis de voz de Google" desde Play Store y elegilo en Ajustes de Android → Salida de texto a voz. Con el botón "Probar voz" verificás al instante. |
| La voz de mujer/hombre suena igual | El motor de la box solo tiene una voz: ConitOS ajusta el tono automáticamente. Con Google TTS instalado se usan voces reales de mujer/hombre. |
| "No hay motor de voz" (para escuchar) | Instalá la app de Google / Google Search desde Play Store, o usá el campo de texto del panel de voz |
| El clima no carga | Verificá Internet; en Ajustes usá "Actualizar clima" |
| El desenfoque se ve menos suave | En Android < 12 se usa desenfoque por software; podés ajustar la intensidad en Ajustes → Desenfoque |
| No arranca al encender | Presioná HOME y elegí "ConitOS · Siempre"; si el firmware lo impide, deshabilitá el launcher del fabricante con ADB (§3) |
| "Reiniciá/Apagá" no funciona | Es normal en apps normales: requiere instalación como app de sistema (§8) |
| Desinstalar | `adb uninstall com.conitos.launcher` |
