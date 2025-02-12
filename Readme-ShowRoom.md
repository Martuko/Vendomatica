# Instalación de SmartPSS/Lite en Linux con Wine y Ejecucion de automatizacion de Showroom

Este documento explica cómo instalar **SmartPSS/Lite** en un sistema Linux utilizando Wine y la Ejecucion y modificacion de la automatizacion de Showroom

# Modificacion y ejecucion de automatizacion

- ** Ubicacion del archivo **
  ```bash
  cd /home/showroom/
  nano start_smartpss.sh
  ```
- ** Contenido del archivo **

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
sleep 1  # Espera un poco más para que se enfoque correctamente

xdotool mousemove 482 252 click 1
sleep 20  # Tiempo para cargar la interfaz de monitoreo

xdotool mousemove 456 304 click 1
xdotool click 1
sleep 15  # Tiempo para cargar la cámara

# 3. Cambiar a pantalla completa (doble clic en la cámara seleccionada)
xdotool mousemove 660 327 click 1
xdotool click 1
sleep 10

# Asegúrate de que la ventana esté activa para el último clic
xdotool windowfocus "$WINDOW_ID"
sleep 1  # Asegura que la ventana está completamente en primer plano
xdotool mousemove 1552 913 click 1
sleep 300
# Aquí empieza el ciclo que cambia al escritorio 2 y lanza VLC en loop

VIDEO_PATH="/home/showroom/VideoVendomatica.mp4"  # Ruta del archivo de video que quieres mostrar

while true; do
    # Cambiar al escritorio 2
    wmctrl -s 1  # Cambiar al escritorio 2

    sleep 2  # Espera un poco para asegurarse de que se cambie de escritorio

    # Ejecuta VLC y reproduce el video en pantalla completa
    cvlc --fullscreen --loop "$VIDEO_PATH" &  # vlc reproduce en loop

    sleep 600  # Espera 10 minutos

    # Cambia de vuelta al escritorio 1
    wmctrl -s 0  # Cambiar al escritorio 1
    xdotool mousemove 150 150 
    pkill vlc
    sleep 600 # Espera un poco para asegurarse de que se cambie de escritorio

    # Cierra VLC antes de repetir el ciclo

done
```
``
