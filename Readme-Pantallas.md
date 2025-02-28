# Automatización de Cambio de Pantallas con Google Chrome en Linux

Este documento explica la automatización de la apertura y rotación de pestañas en diferentes escritorios virtuales de Linux utilizando Google Chrome, `wmctrl` y `xdotool`.

---

## 2. Modificación y Ejecución de la Automatización de Pantallas

### **2.1 Ubicación del archivo de automatización**

El archivo de automatización suele estar ubicado en:

```bash
/home/pantallas/start_screens.sh
```

Para verificar su existencia y ubicación, usa:

```bash
ls /home/pantallas/
```

Para abrirlo y editarlo:

```bash
nano /home/pantallas/automatizacion.sh
```

---

### **2.2 Contenido del archivo de automatización**

El script es el siguiente:

```bash
#!/bin/bash

# Lista de URLs a abrir en cada escritorio
URLS=(
    "https://lookerstudio.google.com/u/0/reporting/98576bd6-16f9-41e8-ad4a-d78c107ba414/page/p_2a5787e0kd?s=rPD-n3Anxu8"
    "https://lookerstudio.google.com/reporting/01931302-18d2-418d-b373-d99ca84a1c4c/page/07mxD"
    "https://lookerstudio.google.com/reporting/d9bf0b40-3545-4774-a6fc-99d4f88a637c/page/p_3cpxt778kd"
    "https://lookerstudio.google.com/reporting/d9bf0b40-3545-4774-a6fc-99d4f88a637c/page/p_94a2p73bnd"
    "https://lookerstudio.google.com/reporting/d9bf0b40-3545-4774-a6fc-99d4f88a637c/page/p_ng0v2lyxgd"
)

# Tiempo de cambio de escritorio después de la primera vuelta
TIEMPO_CAMBIO_NORMAL=60

# Asegurar que Google Chrome esté cerrado antes de empezar
pkill -f google-chrome
sleep 3

# Función para abrir las páginas en cada escritorio y maximizar
abrir_paginas() {
    for i in {0..4}; do
        wmctrl -s $i
        sleep 10
        google-chrome --password-store=basic --profile-directory="Profile 2" --new-window "${URLS[$i]}" &
        
        if [ $i -eq 0 ]; then
            sleep 10
            xdotool mousemove 1889 117 click 1
        elif [[ $i -eq 2 || $i -eq 3 || $i -eq 4 ]]; then
            sleep 10
            xdotool mousemove 235 1053 click 1
        fi
        
        sleep 5
        xdotool search --onlyvisible --class "google-chrome" windowactivate --sync key F11
        sleep 5
    done
}

# Primera pasada con tiempo de carga inicial
abrir_paginas

# Cambio de escritorios en bucle cada 1 minuto
while true; do
    for i in {0..4}; do
        wmctrl -s $i
        sleep $TIEMPO_CAMBIO_NORMAL
    done
done
```

---

### **2.3 Explicación del funcionamiento**

1. **Definición de URLs**: Se establece una lista de 5 URLs de Google Looker Studio.
2. **Cierre de Google Chrome**: Antes de iniciar el proceso, se cierra cualquier instancia de Google Chrome en ejecución con `pkill -f google-chrome`.
3. **Función `abrir_paginas()`**:
   - Cambia al escritorio virtual correspondiente con `wmctrl -s $i`.
   - Espera 10 segundos para asegurarse de que el cambio fue exitoso.
   - Abre Google Chrome con la URL asignada a ese escritorio.
   - Realiza clics en posiciones específicas para interactuar con la interfaz.
   - Activa el modo de pantalla completa (`F11`) en cada ventana de Chrome.
4. **Bucle de rotación de escritorios**:
   - Una vez abiertas todas las páginas, un `while true` mantiene el cambio de escritorios cada 60 segundos (`sleep $TIEMPO_CAMBIO_NORMAL`).

---

### **2.4 Cómo modificar el script para futuras actualizaciones**

#### **Cambiar las URLs de los informes**
Para modificar las URLs que se abren en cada escritorio, edita la sección:

```bash
URLS=(
    "URL_1"
    "URL_2"
    "URL_3"
    "URL_4"
    "URL_5"
)
```

#### **Ajustar el tiempo de rotación de escritorios**
Si deseas cambiar el tiempo de espera entre cambios de escritorio, modifica:

```bash
TIEMPO_CAMBIO_NORMAL=60  # Cambia este valor en segundos
```

Por ejemplo, para cambiar cada 30 segundos:

```bash
TIEMPO_CAMBIO_NORMAL=30
```

#### **Modificar la interacción con la interfaz**
El script usa `xdotool` para realizar clics en posiciones específicas. Si la interfaz cambia, ajusta las coordenadas:

```bash
xdotool mousemove X Y click 1  # Reemplaza X e Y con nuevas coordenadas
```

Para obtener nuevas coordenadas, usa:

```bash
xdotool getmouselocation
```

#### **Ejecutar con otro perfil de Chrome**
Si deseas usar otro perfil de Chrome, cambia:

```bash
google-chrome --profile-directory="Profile 2"
```

Por ejemplo, para usar el perfil predeterminado:

```bash
google-chrome --profile-directory="Default"
```

Para saber el perfil que se esta utilizando en la barra de busqueda de google coloque:
```bash
chrome://version
```
Y busque Perfil en uso o Profile Path

#### **Ejecutar el script automáticamente al inicio**
Si deseas que este script se ejecute automáticamente al iniciar el sistema, agrégalo a `crontab`:

```bash
crontab -e
```

Y añade la siguiente línea al final:

```bash
@reboot /home/pantallas/automatizacion.sh
```

---
