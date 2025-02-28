# Instalación de SmartPSS/Lite en Linux con Wine y Ejecución de Automatización de Showroom

Este documento explica cómo instalar **SmartPSS/Lite** en un sistema Linux utilizando Wine y cómo ejecutar y modificar la automatización de Showroom.

---

## 1. Instalación de SmartPSS/Lite en Linux con Wine

## Requisitos previos

1. **Sistema operativo**: Linux (Debian). https://www.debian.org/download
2. **Permisos de administrador**: Necesitarás permisos de `sudo` para instalar paquetes.
3. **Instalador de SmartPSS/Lite**: Descarga el instalador desde el sitio oficial de Dahua. https://dahuawiki.com/SmartPSS / https://dahuawiki.com/SmartPSS_Lite

---

## Pasos para instalar y ejecutar SmartPSS Lite

### 1. Instalar Wine y Winetricks

Wine es una capa de compatibilidad para ejecutar aplicaciones de Windows en Linux. Winetricks es una herramienta que facilita la instalación de dependencias.

Abre una terminal y ejecuta los siguientes comandos:

```bash
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install wine winetricks
```
### 2. Crear un prefijo de Wine

Un prefijo de Wine es un entorno aislado donde se instalarán las dependencias y la aplicación.

Ejecuta el siguiente comando para crear un prefijo de Wine de 32 bits:

```bash
WINEPREFIX=~/smartpss-wine winecfg
```

Esto creará una carpeta llamada `smartpss-wine` en tu directorio personal.

### 3. Instalar dependencias necesarias

SmartPSS Lite requiere algunas bibliotecas de Windows para funcionar correctamente. Usa Winetricks para instalarlas:

```bash
WINEPREFIX=~/smartpss-wine winetricks d3dx9 d3dx10 vcrun2019 quartz
```

Esto instalará:

- `d3dx9`: Bibliotecas de DirectX 9.
- `d3dx10`: Bibliotecas de DirectX 10.
- `vcrun2019`: Paquete de Visual C++ Redistributable 2019.
- `quartz`: Biblioteca multimedia para la reproducción de video.

### 4. Configurar Wine para usar OpenGL

Para optimizar el rendimiento gráfico, configura Wine para usar OpenGL.

Ejecuta el siguiente comando para abrir la configuración de Wine:

```bash
WINEPREFIX=~/smartpss-wine winecfg
```

En la ventana que aparece:

- Ve a la pestaña **Graphics**.
- Marca la opción **Emulate a virtual desktop**.
- Desmarca **Allow the window manager to decorate the windows**.
- Desmarca **Allow the window manager to control the windows**.
- Reduce la resolución a 1024x768 (opcional, pero recomendado para mejorar el rendimiento).

#### 4.1 Configurar con Vulkan en caso de no funcionar con OpenGL

Si tu sistema tiene una GPU compatible con Vulkan (por ejemplo, NVIDIA o AMD), puedes usar DXVK para mejorar el rendimiento de SmartPSS Lite. Sigue estos pasos:

```bash
WINEPREFIX=~/smartpss-wine winetricks dxvk
```

Esto instalara:

- `Vulkan`: Bibliotecas de Vulkan.

Ejecuta Vulkaninfo para verificar el correcto funcionamiento de Vulkan

```bash
vulkaninfo
```

En caso de no tener instalado Vulkaninfo, Instalalo con:

```bash
sudo apt install vulkan-tools
```

Asegurate de que las bibliotecas de Directx esten sobreescritas por el DXVK, Para eso ejecuta:

```bash
WINEDLLOVERRIDES="d3d9,d3d10" wine SmartPSSLite.exe
```

Ejecuta SmartPSS

### 5. Instalar SmartPSS Lite

Descarga el instalador de SmartPSS/Lite desde el sitio oficial de Dahua y guárdalo en una ubicación conocida (por ejemplo, `~/Descargas`).

Luego, instala SmartPSS Lite en el prefijo de Wine:

```bash
WINEPREFIX=~/smartpss-wine wine ~/Descargas/SmartPSS_Lite_Installer.exe
```

Sigue las instrucciones del instalador para completar la instalación.

### 6. Ejecutar SmartPSS Lite

Una vez instalado, puedes ejecutar SmartPSS Lite desde la terminal:

```bash
WINEPREFIX=~/smartpss-wine wine ~/smartpss-wine/drive_c/Program\ Files/SmartPSS\ Lite/SmartPSS.exe
```

Si prefieres crear un acceso directo en el menú de aplicaciones, puedes usar una herramienta como **menulibre** o crear un archivo `.desktop` manualmente.

---

## Solución de problemas

- **Error al iniciar SmartPSS/Lite**:
  - Verifica que todas las dependencias estén instaladas correctamente.
    
- **Problemas de rendimiento**:
  - Reduce la resolución en `winecfg`.
  - Desactiva efectos gráficos avanzados.

- **Errores relacionados con DirectX**:
  - Asegúrate de que las bibliotecas `d3dx9` y `quartz` estén instaladas.

---

## Notas adicionales

- Si tu sistema tiene una GPU que no soporta Vulkan, no uses DXVK. En su lugar, usa la implementación de DirectX de Wine (basada en OpenGL).
- Si el rendimiento es bajo, considera usar una máquina virtual con Windows o actualizar el hardware del sistema.

---


## 2. Modificación y Ejecución de la Automatización del Showroom

### **2.1 Ubicación del archivo de automatización y del video**

```bash
cd /home/showroom/
ls
```

Para abrir el archivo de automatización:

```bash
nano /home/showroom/start_smartpss.sh  # Editor fácil de usar
# O bien:
vim /home/showroom/start_smartpss.sh  # Editor avanzado
```

Para reproducir el video manualmente:

```bash
cvlc --fullscreen /home/showroom/VideoVendomatica.mp4
```
### **2.2 Contenido del archivo de automatización**

```bash
#!/bin/bash

WINE_EXEC="/usr/bin/wine"  # Ruta al binario de Wine
SMARTPSS_PATH="$HOME/.wine/drive_c/Program Files/SmartPSSLite/SmartPSSLite.exe"

"$WINE_EXEC" "$SMARTPSS_PATH" &  # Inicia SmartPSS Lite

sleep 20 # Espera para la carga del programa

WINDOW_ID=$(xdotool search --name "SmartPSS" | head -n 1)
xdotool windowactivate "$WINDOW_ID"
sleep 1
xdotool windowfocus "$WINDOW_ID"
sleep 1

# Abre la interfaz de monitoreo
xdotool mousemove 482 252 click 1
sleep 20  # Espera para cargar la interfaz

# Selecciona la cámara
xdotool mousemove 456 304 click 1
xdotool click 1
sleep 15  # Espera para la carga de la cámara

# Cambia a pantalla completa (doble clic en la cámara seleccionada)
xdotool mousemove 660 327 click 1
xdotool click 1
sleep 10

# Confirma que la ventana esté activa antes del último clic
xdotool windowfocus "$WINDOW_ID"
sleep 1  
xdotool mousemove 1552 913 click 1
sleep 300  # Mantiene la vista por 5 minutos

# Inicia el loop que alterna entre SmartPSS y el video promocional
VIDEO_PATH="/home/showroom/VideoVendomatica.mp4"

while true; do
    wmctrl -s 1  # Cambia al escritorio 2
    sleep 2  
    cvlc --fullscreen --loop "$VIDEO_PATH" &  # Ejecuta VLC en loop
    sleep 600  # Espera 10 minutos
    wmctrl -s 0  # Cambia de vuelta al escritorio 1
    xdotool mousemove 150 150 
    pkill vlc  # Cierra VLC antes de repetir el ciclo
    sleep 600 # Espera antes de volver a mostrar SmartPSS

done
```

---
## 2.3 Explicación del funcionamiento

1. **Ejecuta SmartPSS Lite** usando Wine.
2. **Automatiza la interfaz gráfica** con `xdotool` para abrir la cámara en pantalla completa.
3. **Alterna entre SmartPSS y un video promocional** en VLC cada 10 minutos.
4. **Usa `wmctrl` y `xdotool`** para controlar las ventanas y simular interacciones.
5. **Se ejecuta en un bucle infinito** para mantener la automatización funcionando indefinidamente.

---
### **2.4 Cómo modificar el script para futuras actualizaciones**

#### **Cambiar la ubicación del programa SmartPSS**
Si SmartPSS se instaló en otra carpeta, edita `start_smartpss.sh` y cambia la siguiente línea:

```bash
SMARTPSS_PATH="$HOME/.wine/drive_c/Program Files/SmartPSSLite/SmartPSSLite.exe"
```

Si la nueva ruta es `/opt/SmartPSS/SmartPSSLite.exe`, cámbiala así:

```bash
SMARTPSS_PATH="/opt/SmartPSS/SmartPSSLite.exe"
```

#### **Ajustar tiempos de espera**
Los tiempos de espera (`sleep X`) determinan cuántos segundos esperar entre cada acción.

Por ejemplo, si SmartPSS tarda más en cargar, edita esta línea:

```bash
sleep 20  # Espera 20 segundos antes de continuar
```

Si necesitas esperar más, cámbialo a:

```bash
sleep 30  # Espera 30 segundos
```

#### **Modificar las posiciones del ratón**
Si la interfaz cambia, ajusta las coordenadas en `xdotool`.

Ejemplo actual:

```bash
xdotool mousemove 482 252 click 1  # Abre la interfaz de monitoreo
```

Para mover el clic a una nueva posición, usa esta sintaxis:

```bash
xdotool mousemove X Y click 1  # Reemplaza X e Y con nuevas coordenadas
```

Para encontrar nuevas coordenadas, usa este comando y mueve el cursor:

```bash
xdotool getmouselocation
```

#### **Usar un video diferente**
Si necesitas cambiar el video de showroom, edita `VIDEO_PATH` en `start_smartpss.sh`:

```bash
VIDEO_PATH="/home/showroom/VideoVendomatica.mp4"
```

Si el nuevo video se llama `NuevoVideo.mp4`:

```bash
VIDEO_PATH="/home/showroom/NuevoVideo.mp4"
```

#### **Ajustar la duración de la visualización del video**
El script alterna entre SmartPSS y el video cada 10 minutos (`sleep 600`). Para cambiarlo:

- Para 5 minutos:
  ```bash
  sleep 300  # Espera 5 minutos
  ```
- Para 15 minutos:
  ```bash
  sleep 900  # Espera 15 minutos
  ```

#### **Alternar con más programas**
Si necesitas ejecutar otro programa en el bucle, agrégalo antes de `wmctrl -s 0`.

Ejemplo: para abrir un navegador con una URL:

```bash
firefox --new-window "https://www.tupagina.com" &
```

#### **Registro de logs para depuración**
Si quieres registrar cada ejecución en un archivo de log, agrega esta línea:

```bash
echo "Ejecutando SmartPSS a las $(date)" >> /home/showroom/logs.txt
```

Colócala después de iniciar SmartPSS para registrar cada vez que se ejecuta:

```bash
"$WINE_EXEC" "$SMARTPSS_PATH" &
echo "Ejecutando SmartPSS a las $(date)" >> /home/showroom/logs.txt
```

---



