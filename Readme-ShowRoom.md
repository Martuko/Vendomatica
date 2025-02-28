# Instalación de SmartPSS/Lite en Linux con Wine y Ejecución de Automatización de Showroom

Este documento explica cómo instalar **SmartPSS/Lite** en un sistema Linux utilizando Wine y cómo ejecutar y modificar la automatización de Showroom.

---

## 1. Instalación de SmartPSS/Lite en Linux con Wine

### **1.1 Requisitos previos**

1. **Sistema operativo**: Linux (Debian). https://www.debian.org/download
2. **Permisos de administrador**: Necesitarás permisos de `sudo` para instalar paquetes.
3. **Instalador de SmartPSS/Lite**: Descarga el instalador desde el sitio oficial de Dahua. https://dahuawiki.com/SmartPSS / https://dahuawiki.com/SmartPSS_Lite

### **1.2 Instalación de Wine y Winetricks**

Wine es una capa de compatibilidad para ejecutar aplicaciones de Windows en Linux. 

```bash
sudo apt update
sudo apt install wine winetricks
```

### **1.3 Creación de un prefijo de Wine**

```bash
WINEPREFIX=~/smartpss-wine winecfg
```

### **1.4 Instalación de dependencias necesarias**

```bash
WINEPREFIX=~/smartpss-wine winetricks d3dx9 d3dx10 vcrun2019 quartz
```

### **1.5 Configuración de Wine para usar OpenGL**

```bash
WINEPREFIX=~/smartpss-wine winecfg
```

En la pestaña **Graphics**:
- Marca **Emulate a virtual desktop**.
- Desmarca **Allow the window manager to decorate the windows**.
- Desmarca **Allow the window manager to control the windows**.
- Reduce la resolución a 1024x768.

### **1.6 Instalación de SmartPSS Lite**

```bash
WINEPREFIX=~/smartpss-wine wine ~/Descargas/SmartPSS_Lite_Installer.exe
```

### **1.7 Ejecución de SmartPSS Lite**

```bash
WINEPREFIX=~/smartpss-wine wine ~/smartpss-wine/drive_c/Program\ Files/SmartPSS\ Lite/SmartPSS.exe
```

---

## 2. Modificación y Ejecución de la Automatización del Showroom

### **2.1 Ubicación del archivo de automatización y del video**

```bash
cd /home/showroom/
start_smartpss.sh
```

Ubicación del video:

```bash
cd /home/showroom/
VideoVendomatica.mp4
```

Abrir archivo de automatización y video:

```bash
nano /home/showroom/start_smartpss.sh
vim /home/showroom/start_smartpss.sh
cvlc /home/showroom/VideoVendomatica.mp4
```

### **2.2 Contenido del archivo de automatización**

```bash
#!/bin/bash

WINE_EXEC="/usr/bin/wine"  # Ruta al binario de Wine
SMARTPSS_PATH="$HOME/.wine/drive_c/Program Files/SmartPSSLite/SmartPSSLite.exe"

"$WINE_EXEC" "$SMARTPSS_PATH" &

sleep 20 # Espera de carga del Programa

WINDOW_ID=$(xdotool search --name "SmartPSS" | head -n 1)
xdotool windowactivate "$WINDOW_ID"
sleep 1
xdotool windowfocus "$WINDOW_ID"
sleep 1

xdotool mousemove 482 252 click 1
sleep 20  # Tiempo para cargar la interfaz de monitoreo

xdotool mousemove 456 304 click 1
xdotool click 1
sleep 15  # Tiempo para cargar la cámara

xdotool mousemove 660 327 click 1
xdotool click 1
sleep 10

xdotool windowfocus "$WINDOW_ID"
sleep 1  
xdotool mousemove 1552 913 click 1
sleep 300

VIDEO_PATH="/home/showroom/VideoVendomatica.mp4"

while true; do
    wmctrl -s 1  # Cambiar al escritorio 2
    sleep 2  
    cvlc --fullscreen --loop "$VIDEO_PATH" &  # VLC en loop
    sleep 600  # Espera 10 minutos
    wmctrl -s 0  # Cambiar al escritorio 1
    xdotool mousemove 150 150 
    pkill vlc
    sleep 600 # Espera 10 minutos antes de repetir

done
```

---

## 3. Explicación del funcionamiento

1. **Ejecuta SmartPSS Lite** usando Wine.
2. **Automatiza la interfaz gráfica** con `xdotool` para abrir la cámara en pantalla completa.
3. **Alterna entre SmartPSS y un video promocional** en VLC cada 10 minutos.
4. **Usa `wmctrl` y `xdotool`** para controlar las ventanas y simular interacciones.
5. **Se ejecuta en un bucle infinito** para mantener la automatización funcionando indefinidamente.

---

## 4. Solución de problemas

- **Error al iniciar SmartPSS/Lite**:
  - Verifica que todas las dependencias estén instaladas correctamente.
- **Problemas de rendimiento**:
  - Reduce la resolución en `winecfg`.
  - Desactiva efectos gráficos avanzados.
- **Errores relacionados con DirectX**:
  - Asegúrate de que las bibliotecas `d3dx9` y `quartz` estén instaladas.

---

## 5. Mejoras futuras

✅ **Parámetros configurables**: Permitir definir rutas y tiempos de espera como variables.  
✅ **Manejo de errores**: Reiniciar SmartPSS o VLC automáticamente en caso de falla.  
✅ **Registros de ejecución**: Agregar logs (`echo` a un archivo) para monitorear la automatización.  

---

Este README proporciona una guía completa para la instalación y automatización de SmartPSS Lite en Linux. ¡Si necesitas modificar algo o agregar más detalles, házmelo saber! 🚀
