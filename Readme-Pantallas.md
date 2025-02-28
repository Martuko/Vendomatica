# Automatización de Páginas de Google Chrome en Múltiples Escritorios en Linux

Este documento explica el funcionamiento y la personalización de un script en **Bash** que abre varias páginas web en Google Chrome en diferentes escritorios virtuales de un entorno Linux. Además, configura cada ventana en pantalla completa y cambia de escritorio de manera automatizada.

---

## 1. Descripción General del Script

Este script realiza las siguientes acciones:

1. **Cierra Google Chrome** antes de comenzar para evitar conflictos.
2. **Abre cinco URLs diferentes** en distintas ventanas de Chrome, cada una en un escritorio virtual diferente.
3. **Maximiza cada ventana** y la pone en pantalla completa.
4. **Cambia de escritorio cada 60 segundos** en un bucle infinito.

---

## 2. Instalación de Dependencias

Para que el script funcione correctamente, asegúrate de tener instaladas las siguientes herramientas:

```bash
sudo apt update
sudo apt install wmctrl xdotool google-chrome-stable
```

- **wmctrl**: Permite cambiar entre escritorios virtuales.
- **xdotool**: Simula movimientos del mouse y teclado.
- **google-chrome-stable**: Navegador Google Chrome.

---

## 3. Explicación del Código

### **3.1 Definición de Variables**

```bash
# Lista de URLs a abrir en cada escritorio
URLS=(
    "https://lookerstudio.google.com/u/0/reporting/98576bd6-16f9-41e8-ad4a-d78c107ba414/page/p_2a5787e0kd?s=rPD-n3Anxu8"
    "https://lookerstudio.google.com/reporting/01931302-18d2-418d-b373-d99ca84a1c4c/page/07mxD"
    "https://lookerstudio.google.com/reporting/d9bf0b40-3545-4774-a6fc-99d4f88a637c/page/p_3cpxt778kd"
    "https://lookerstudio.google.com/reporting/d9bf0b40-3545-4774-a6fc-99d4f88a637c/page/p_94a2p73bnd"
    "https://lookerstudio.google.com/reporting/d9bf0b40-3545-4774-a6fc-99d4f88a637c/page/p_ng0v2lyxgd"
)

# Tiempo de espera antes de cambiar de escritorio (60 segundos por defecto)
TIEMPO_CAMBIO_NORMAL=60
```

- `URLS`: Lista de enlaces que se abrirán en los distintos escritorios.
- `TIEMPO_CAMBIO_NORMAL`: Tiempo en segundos que el script esperará antes de cambiar de escritorio.

---

### **3.2 Cierre de Google Chrome al iniciar**

```bash
# Asegurar que Google Chrome esté cerrado antes de empezar
pkill -f google-chrome
sleep 3
```

- Se usa `pkill -f google-chrome` para cerrar cualquier instancia abierta de Chrome.
- `sleep 3`: Espera 3 segundos antes de continuar.

---

### **3.3 Función para abrir las páginas**

```bash
abrir_paginas() {
    for i in {0..4}; do
        # Cambiar al escritorio correspondiente
        wmctrl -s $i
        sleep 10

        # Abrir Google Chrome con la URL correspondiente
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
```

#### **Explicación paso a paso**:

1. **`wmctrl -s $i`**: Cambia al escritorio `i` antes de abrir la URL correspondiente.
2. **`sleep 10`**: Espera 10 segundos para permitir la carga del escritorio.
3. **`google-chrome --new-window "${URLS[$i]}"`**: Abre Google Chrome con la URL de la lista.
4. **Simulación de clic con `xdotool`**:
    - En el escritorio `0`, hace clic en una coordenada específica (`1889 117`).
    - En los escritorios `2`, `3` y `4`, hace clic en otra posición (`235 1053`).
5. **Maximización en pantalla completa** con `xdotool key F11`.
6. **`sleep 5`**: Pausa antes de procesar la siguiente ventana.

---

### **3.4 Cambio de escritorios en bucle**

```bash
while true; do
    for i in {0..4}; do
        wmctrl -s $i
        sleep $TIEMPO_CAMBIO_NORMAL
    done
done
```

- `while true; do`: Mantiene el script en ejecución indefinida.
- `for i in {0..4}; do`: Itera sobre los escritorios disponibles.
- `wmctrl -s $i`: Cambia al escritorio `i`.
- `sleep $TIEMPO_CAMBIO_NORMAL`: Espera el tiempo configurado antes de pasar al siguiente escritorio.

---

## 4. Cómo Modificar el Script

### **4.1 Cambiar las URLs**

Edita la lista `URLS` y reemplázalas con los enlaces deseados.

```bash
URLS=(
    "https://tupagina.com/reporte1"
    "https://tupagina.com/reporte2"
    "https://tupagina.com/reporte3"
    "https://tupagina.com/reporte4"
    "https://tupagina.com/reporte5"
)
```

### **4.2 Ajustar el Tiempo de Cambio**

Modifica `TIEMPO_CAMBIO_NORMAL` para ajustar el tiempo en segundos:

```bash
TIEMPO_CAMBIO_NORMAL=120  # Ahora cambiará cada 2 minutos
```

### **4.3 Modificar las Coordenadas del Clic**

Si las posiciones de los botones cambian, usa este comando para encontrar nuevas coordenadas:

```bash
xdotool getmouselocation
```

Reemplaza los valores en:

```bash
xdotool mousemove X Y click 1
```

---

## 5. Ejecución del Script

Guarda el archivo como `automatizacion.sh`, dale permisos de ejecución y ejecútalo:

```bash
chmod +x automation.sh
./automatizacion.sh
```

