# 💡 esp32-servo-switch iot-light-control


**IoT light switch controller — an ESP32 running MicroPython hosts its own web server and physically flips a real wall switch via a servo motor, triggered from any browser on the local network.**

[![License](https://img.shields.io/badge/License-MIT-lightgrey)](LICENSE)
![Platform](https://img.shields.io/badge/Platform-ESP32%20%7C%20MicroPython-blue)
![Protocol](https://img.shields.io/badge/Protocol-HTTP%20%7C%20WiFi-green)
[![Demo](https://img.shields.io/badge/Demo-YouTube-red)](https://www.youtube.com/watch?v=7TutiHulW4s)

**🌐 Language / Idioma:** &nbsp; [🇬🇧 English](#-english) &nbsp;|&nbsp; [🇨🇴 Español](#-español)

---

## 🇬🇧 English

### How it works

```
Browser (any device on the LAN)
        │
        │  HTTP GET  /?led=on  or  /?led=off
        ▼
┌─────────────────────────────────┐
│  ESP32 — MicroPython web server │
│  Port 80                        │
│  Serves CSS3 toggle switch UI   │
└──────────────┬──────────────────┘
               │
               ├── Pin 27 → Servo motor → physically flips wall switch
               └── Pin 13 → Status LED (on = switch is ON)
```

The ESP32 runs entirely standalone — no cloud, no MQTT broker, no external service. On boot it connects to your WiFi network and starts a raw HTTP server on port 80. Any device on the same network can open the IP address in a browser and toggle the switch through a CSS3 web UI that looks and feels like a physical switch.

When the button is pressed:
- The browser sends a GET request to `/?led=on` or `/?led=off`
- The ESP32 parses the request, drives the servo to the target angle, and updates the status LED
- The page reloads reflecting the new state

### Hardware

| Component | Pin | Notes |
|---|---|---|
| Servo motor | GPIO 27 | Physically actuates the wall switch lever |
| Status LED | GPIO 13 | ON when the switch is in the ON position |

**Servo angles:**

| State | Angle | Action |
|---|---|---|
| ON | 0° | Pushes switch to ON position |
| OFF | 135° | Pushes switch to OFF position |

The servo function drives the signal pin with software PWM — 50 pulses at the target duty cycle — then stops. It does not hold position, so the switch lever itself maintains the physical state.

### Project Structure

```
├── firmware/
│   ├── boot.py       # WiFi connection — runs once on startup
│   └── main.py       # Web server + servo control loop
└── web_template/
    ├── index.html    # Reference HTML for the CSS3 switch UI
    └── style.css     # Reference stylesheet (inlined into main.py for deployment)
```

> **Note:** The web UI (HTML + CSS) is inlined as a string inside `main.py` for deployment on the ESP32 filesystem. The `web_template/` folder contains the separated reference versions for readability.

### Setup

**1. Flash MicroPython onto your ESP32** — download the firmware from [micropython.org](https://micropython.org/download/esp32/) and flash it with `esptool`:

```bash
pip install esptool
esptool.py --port /dev/ttyUSB0 erase_flash
esptool.py --port /dev/ttyUSB0 write_flash -z 0x1000 esp32-firmware.bin
```

**2. Configure WiFi credentials** in `boot.py`:

```python
ssid = 'your_network_name'
password = 'your_password'
```

**3. Upload files to the ESP32** — using [Thonny](https://thonny.org/) or `ampy`:

```bash
pip install adafruit-ampy
ampy --port /dev/ttyUSB0 put firmware/boot.py
ampy --port /dev/ttyUSB0 put firmware/main.py
```

**4. Reset the ESP32.** The IP address will print to the serial console on successful WiFi connection.

**5. Open the IP address in any browser** on the same network.

### Configuration

| Parameter | Location | Default | Notes |
|---|---|---|---|
| WiFi SSID | `boot.py` | `'nombre de la red aqui'` | Replace with your network name |
| WiFi password | `boot.py` | `'contrasena aqui'` | Replace with your password |
| Servo pin | `main.py` | GPIO 27 | Change to match your wiring |
| Status LED pin | `boot.py` | GPIO 13 | Change to match your wiring |
| Servo ON angle | `main.py` | 0° | Adjust to match your switch geometry |
| Servo OFF angle | `main.py` | 135° | Adjust to match your switch geometry |
| Server port | `main.py` | 80 | Standard HTTP — no port needed in browser URL |

---

[🇨🇴 Leer en Español](#-español)

---

## 🇨🇴 Español

[🇬🇧 Read in English](#-english)

### Cómo funciona

```
Navegador (cualquier dispositivo en la red local)
        │
        │  HTTP GET  /?led=on  o  /?led=off
        ▼
┌─────────────────────────────────────┐
│  ESP32 — servidor web MicroPython   │
│  Puerto 80                          │
│  Sirve interfaz de interruptor CSS3 │
└──────────────┬──────────────────────┘
               │
               ├── Pin 27 → Servo motor → acciona físicamente el interruptor
               └── Pin 13 → LED de estado (encendido = interruptor en ON)
```

El ESP32 funciona de forma completamente autónoma — sin nube, sin broker MQTT, sin servicios externos. Al encenderse se conecta a la red WiFi e inicia un servidor HTTP en el puerto 80. Cualquier dispositivo en la misma red puede abrir la IP en el navegador y controlar el interruptor a través de una interfaz web CSS3 que simula un interruptor físico real.

Al presionar el botón:
- El navegador envía una solicitud GET a `/?led=on` o `/?led=off`
- El ESP32 analiza la solicitud, mueve el servo al ángulo objetivo y actualiza el LED de estado
- La página se recarga mostrando el nuevo estado

### Hardware

| Componente | Pin | Notas |
|---|---|---|
| Servo motor | GPIO 27 | Acciona físicamente la palanca del interruptor de pared |
| LED de estado | GPIO 13 | Encendido cuando el interruptor está en posición ON |

**Ángulos del servo:**

| Estado | Ángulo | Acción |
|---|---|---|
| ON | 0° | Empuja el interruptor a la posición ON |
| OFF | 135° | Empuja el interruptor a la posición OFF |

La función del servo genera la señal PWM por software — 50 pulsos al ciclo de trabajo objetivo — y luego se detiene. No mantiene la posición, por lo que la palanca del interruptor conserva el estado físico por sí misma.

### Estructura del proyecto

```
├── firmware/
│   ├── boot.py       # Conexión WiFi — se ejecuta una vez al encender
│   └── main.py       # Servidor web + bucle de control del servo
└── web_template/
    ├── index.html    # HTML de referencia para la interfaz CSS3
    └── style.css     # Hoja de estilos de referencia (incrustada en main.py para despliegue)
```

> **Nota:** La interfaz web (HTML + CSS) está incrustada como string dentro de `main.py` para el despliegue en el sistema de archivos del ESP32. La carpeta `web_template/` contiene las versiones separadas para mayor legibilidad.

### Configuración

**1. Flashear MicroPython en el ESP32** — descarga el firmware desde [micropython.org](https://micropython.org/download/esp32/) y flashealo con `esptool`:

```bash
pip install esptool
esptool.py --port /dev/ttyUSB0 erase_flash
esptool.py --port /dev/ttyUSB0 write_flash -z 0x1000 esp32-firmware.bin
```

**2. Configura las credenciales WiFi** en `boot.py`:

```python
ssid = 'nombre_de_tu_red'
password = 'tu_contrasena'
```

**3. Sube los archivos al ESP32** — usando [Thonny](https://thonny.org/) o `ampy`:

```bash
pip install adafruit-ampy
ampy --port /dev/ttyUSB0 put firmware/boot.py
ampy --port /dev/ttyUSB0 put firmware/main.py
```

**4. Reinicia el ESP32.** La dirección IP se imprimirá en la consola serie al conectarse exitosamente al WiFi.

**5. Abre la dirección IP en cualquier navegador** de la misma red.

### Parámetros de configuración

| Parámetro | Ubicación | Valor por defecto | Notas |
|---|---|---|---|
| SSID WiFi | `boot.py` | `'nombre de la red aqui'` | Reemplaza con el nombre de tu red |
| Contraseña WiFi | `boot.py` | `'contrasena aqui'` | Reemplaza con tu contraseña |
| Pin del servo | `main.py` | GPIO 27 | Cambia según tu conexión |
| Pin LED de estado | `boot.py` | GPIO 13 | Cambia según tu conexión |
| Ángulo ON del servo | `main.py` | 0° | Ajusta según la geometría de tu interruptor |
| Ángulo OFF del servo | `main.py` | 135° | Ajusta según la geometría de tu interruptor |
| Puerto del servidor | `main.py` | 80 | HTTP estándar — no se necesita puerto en la URL |

---

## Author / Autor

**Gilberto Galvis Giraldo**

---

## License / Licencia

MIT — see [LICENSE](LICENSE) for details.
