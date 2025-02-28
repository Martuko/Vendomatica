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

# Asegurar que Firefox esté cerrado antes de empezar
pkill -f google-chrome
sleep 3

# Función para abrir las páginas en cada escritorio y maximizar
abrir_paginas() {
    for i in {0..4}; do
        # Cambiar al escritorio correspondiente
        wmctrl -s $i
        sleep 10

        # Abrir Firefox en una nueva ventana con la URL
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
