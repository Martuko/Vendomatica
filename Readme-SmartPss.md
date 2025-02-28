# Instalación y ejecución de SmartPSS Lite en Linux con Wine

Este documento explica cómo instalar y ejecutar **SmartPSS Lite** en un sistema Linux utilizando Wine.

---

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

