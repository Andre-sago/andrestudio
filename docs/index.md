# Andre Sanchez 

Bienvenido 👋  
Esta es una **plantilla** basada en [MkDocs](https://www.mkdocs.org/) + [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) para cursos y proyectos.

---


https://github.com/user-attachments/assets/ec7fd998-ff05-4d02-bd98-d89672494481




https://github.com/user-attachments/assets/d4922ccd-4ad0-469a-8059-b94879381198

Objetivo: Mostrar cómo haces que un motor cambie de dirección (gire hacia un lado y luego al contrario).

Materiales:

Motor DC

Fuente de 5V o 12V (según el motor)

Puente H (por ejemplo, L293D o L298N) 

Arduino (opcional)

Cables

Se muestra el motor girando hacia un sentido.

Luego cambia la polaridad (o manda señal inversa desde Arduino).

Se debe ver claramente que gira hacia el otro lado.



https://github.com/user-attachments/assets/05784511-1efe-449d-b737-7d5d040a7ca7

Servo motor 0° - 10° - 0° - 20°

Qué quiero mostrar:
Que puedo mover el servo a diferentes posiciones usando Arduino.

Materiales:

1 servo 

1 Arduino

Cables jumper


Aquí el servo está conectado al pin 9.
Va a moverse de 0 grados a 10, regresa a 0 y luego a 20.
Así podemos controlar su posición exacta.


https://github.com/user-attachments/assets/326342b8-35cc-4fcb-9ba5-9b7911d7d537

Motor con cambio de velocidad

Qué quiero mostrar:
Que el motor puede ir despacio y luego más rápido.

Materiales:

1 motor DC

1 Arduino

1 módulo L298N o transistor

Cables


Aquí el motor está conectado al pin 9 del Arduino, que es un pin PWM.
Primero va lento, después más rápido y al final a máxima velocidad.






https://github.com/user-attachments/assets/c1ed7cfd-c827-45a6-8024-0a384f7a08e4


![WhatsApp Image 2025-10-15 at 7 18 31 PM](https://github.com/user-attachments/assets/2501a92b-7dd1-495e-ac79-896cab4bf9bb)

Durante esta semana logramos construir un pequeño coche para participar en una competencia que se llevó a cabo el 17 de octubre.
Tuvimos aproximadamente dos semanas para planear y realizar el proyecto.

Para la estructura del coche, utilizamos una base de MDF cortada a medida, llantas unidireccionales, un portapilas, dos puentes H, cables, pilas, y una programación eficiente que permitió controlar correctamente el movimiento del vehículo.



# Robot con Visión por Computadora y Control PID
 
## Descripción General
 
Este proyecto consiste en un **robot balanceador con plataforma móvil**, controlado mediante **visión por computadora (OpenCV)** y estabilizado con **dos servomotores MG996R**. El sistema detecta una **pelota azul** sobre una plataforma negra y ajusta la inclinación mediante un **control PID en tiempo real**.
 
El robot fue construido utilizando:
 
* **2 servomotores MG996R de 15 kg**
* **Estructura y articulaciones impresas en 3D**
* **Plataforma de MDF**
* **ESP32 con comunicación por Bluetooth**
* **Python + OpenCV para visión por computadora**
 
---
 
## Autores
 
* Eduardo Salamanca
* Juan Carlos Pérez
* André Sánchez
* Sebastián Sánchez
 
---
 
## Características Principales
 
* Detección de **plataforma negra** mediante segmentación HSV.
* Detección de **pelota azul** y cálculo preciso del centro.
* Control PID ajustable en tiempo real mediante **sliders en OpenCV**.
* Comunicación serial Bluetooth con ESP32.
* Movimientos suaves gracias a filtros y suavizado.
 
---
 
## Enlace al Video de Funcionamiento
 
[Video demostrativo del robot](https://youtu.be/BvbTCJa57hE?si=qvqjtdl9Kl7VjMze)
 
> Demostración del sistema detectando la pelota, calculando la posición y moviendo los servomotores en tiempo real.
 
---
 
## Hardware Utilizado
 
|            Componente | Cant. | Nota                    |
| --------------------: | :---: | ----------------------- |
|     Servomotor MG996R |   2   | 15 kg torque            |
|                 ESP32 |   1   | Control y comunicación  |
|        Plataforma MDF |   1   | Soporte físico          |
| Piezas impresas en 3D |   4+  | Articulaciones y brazos |
 
---
 
## Software Utilizado
 
* **Python 3.10+**
* **OpenCV**
* **NumPy**
* **PySerial**
* **Arduino IDE**
 
---
 
## Funcionamiento
 
### 1. Detección de Plataforma
 
Se segmenta el área negra mediante un threshold en HSV:
 
```python
lower_black = np.array([0, 0, 0])
upper_black = np.array([180, 255, THRESHOLD_PLATAFORMA])
```
 
Se obtiene el contorno más grande y su centro.
 
### 2. Detección de Pelota Azul
 
```python
LOW_BLUE = np.array([100, 150, 70])
HIGH_BLUE = np.array([130, 255, 255])
```
 
Se calcula centro y radio para validar detección.
 
### 3. Cálculo de Error
 
```python
error_x = centro_pelota.x - centro_plataforma.x
error_y = centro_pelota.y - centro_plataforma.y
```
 
### 4. Control PID
 
```python
output_x = Kp*error_x + Ki*integral_x + Kd*derivative_x
```
 
Sliders en OpenCV permiten ajustar Kp, Ki y Kd.
 
### 5. Envío al ESP32
 
```python
mensaje = f"{int(current_x)},{int(current_y)}\n"
esp32.write(mensaje.encode())
```
 
---
 
## Mecánica
![Multimedia](https://github.com/user-attachments/assets/925b56c5-983c-4463-8b97-83ee4fd21169)![Multimedia (1)](https://github.com/user-attachments/assets/f7d7f766-1d27-45a5-99fc-aa2649a0c96f)![Foto seleccionada](https://github.com/user-attachments/assets/6f1e95bc-75f0-48e3-ade8-899db7e5f94b)
 
---
## Código P
 
Python (Visión por Computadora)
 
```python
import cv2
import time
import numpy as np
import serial
import serial.tools.list_ports
 
# ------------------- Configuración Serial Bluetooth -------------------
esp32_port = 'COM3'
baud_rate = 115200
 
print("=" * 50)
print("Intentando conectar con ESP32...")
print(f"Puerto: {esp32_port} | Baudios: {baud_rate}")
 
def listar_puertos():
    puertos = serial.tools.list_ports.comports()
    print("\n Puertos COM disponibles:")
    if len(puertos) == 0:
        print("  No se encontraron puertos COM")
    for puerto in puertos:
        print(f"   • {puerto.device}: {puerto.description}")
    print()
 
listar_puertos()
 
try:
    esp32 = serial.Serial(esp32_port, baud_rate, timeout=1)
    time.sleep(2)
    print(f" ¡Conectado al ESP32 en {esp32_port}!")
except serial.SerialException as e:
    print(f" Error de conexión serial: {e}")
    print("\n Posibles soluciones:")
    print("   1. Verifica que el puerto COM sea correcto")
    print("   2. Cierra el IDE de Arduino si está abierto")
    print("   3. Cierra cualquier monitor serial activo")
    print("   4. Desconecta y reconecta el ESP32")
    print("   5. Verifica que el ESP32 esté encendido")
    esp32 = None
except Exception as e:
    print(f" Error inesperado: {e}")
    esp32 = None
 
print("=" * 50)
 
# ------------------- Configuración cámara -------------------
cap = cv2.VideoCapture(1)
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)
 
if not cap.isOpened():
    print("Error: No se pudo abrir la cámara")
    exit()
 
# ------------------- Posición inicial servos -------------------
center_angle = 50
current_x = center_angle
current_y = center_angle
DEAD_ZONE = 15  # Zona muerta reducida
smoothing = 0.3  # Suavizado reducido para respuesta más rápida
 
# ------------------- Parámetros PID ajustables -------------------
Kp = 0.15
Ki = 0.001
Kd = 0.20
 
prev_error_x = 0
prev_error_y = 0
integral_x = 0
integral_y = 0
 
MAX_INTEGRAL = 50
 
# ------------------- Parámetros de detección -------------------
# Threshold para plataforma NEGRA (0-255, valor V en HSV)
THRESHOLD_PLATAFORMA = 120  # Ampliado para detectar más tonos
AREA_MIN_PLATAFORMA = 1000  # Área mínima del cuadrado
 
# Rango HSV para pelota azul
LOW_BLUE = np.array([100, 150, 70])
HIGH_BLUE = np.array([130, 255, 255])
AREA_MIN_PELOTA = 200
RADIO_MIN_PELOTA = 8
 
# ------------------- Función para limitar valores -------------------
def constrain(value, min_val, max_val):
    return max(min_val, min(max_val, value))
 
# ------------------- Callbacks para sliders -------------------
def update_kp(val):
    global Kp
    Kp = val / 100.0  # Slider 0-100, valor real 0.00-1.00
    print(f"Kp = {Kp:.3f}")
 
def update_ki(val):
    global Ki
    Ki = val / 1000.0  # Slider 0-100, valor real 0.000-0.100
    print(f"Ki = {Ki:.4f}")
 
def update_kd(val):
    global Kd
    Kd = val / 100.0  # Slider 0-100, valor real 0.00-1.00
    print(f"Kd = {Kd:.3f}")
 
# ------------------- Crear ventana de control -------------------
cv2.namedWindow('Control PID')
cv2.createTrackbar('Kp x100', 'Control PID', int(Kp * 100), 100, update_kp)
cv2.createTrackbar('Ki x1000', 'Control PID', int(Ki * 1000), 100, update_ki)
cv2.createTrackbar('Kd x100', 'Control PID', int(Kd * 100), 100, update_kd)
 
prev_time = time.time()
print("=" * 50)
print("Sistema de Balance: Plataforma + Pelota")
print("=" * 50)
print("CONFIGURACIÓN:")
print(f"  • Centro servos: {center_angle}° (Rango: 0-180°)")
print(f"  • Zona muerta: ±{DEAD_ZONE} píxeles")
print(f"  • Suavizado: {smoothing}")
print(f"  • Threshold plataforma: {THRESHOLD_PLATAFORMA}")
print(f"  • PID: Kp={Kp} Ki={Ki} Kd={Kd}")
print("\nDETECCIÓN:")
print("  • PLATAFORMA NEGRA (HSV): Detecta área más grande de tonos oscuros")
print("  • PELOTA AZUL: Posición para calcular error")
print("  • Usa teclas '1'/'2' para ajustar threshold")
print("\nCONTROLES:")
print("  • 'q' → Salir")
print("  • 'c' → Resetear integrales")
print("  • '1' → Threshold -5")
print("  • '2' → Threshold +5")
print("  • Sliders → Ajustar PID en tiempo real")
print("=" * 50)
 
frame_count = 0
fps_time = time.time()
fps = 0
 
while True:
    ret, frame = cap.read()
    if not ret:
        print("Error: No se pudo leer frame de la cámara")
        break
        
    frame = cv2.flip(frame, 1)
    height, width = frame.shape[:2]
    centrox, centroy = width//2, height//2
 
    # ===============================================================
    # DETECCIÓN 1: PLATAFORMA NEGRA (HSV) - ÁREA MÁS GRANDE
    # ===============================================================
    hsv_plat = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    
    lower_black = np.array([0, 0, 0])
    upper_black = np.array([180, 255, THRESHOLD_PLATAFORMA])
    
    mask_plataforma = cv2.inRange(hsv_plat, lower_black, upper_black)
    
    kernel_plat = np.ones((7,7), np.uint8)
    mask_plataforma = cv2.morphologyEx(mask_plataforma, cv2.MORPH_CLOSE, kernel_plat)
    mask_plataforma = cv2.morphologyEx(mask_plataforma, cv2.MORPH_OPEN, kernel_plat)
    mask_plataforma = cv2.dilate(mask_plataforma, kernel_plat, iterations=1)
    
    contours_plat, _ = cv2.findContours(mask_plataforma, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    area_max_plat = 0
    contorno_plat = None
    centro_plataforma = None
    rectangulo_info = None
    
    for c in contours_plat:
        area = cv2.contourArea(c)
        if area > area_max_plat and area > AREA_MIN_PLATAFORMA:
            area_max_plat = area
            contorno_plat = c
            rectangulo_info = cv2.minAreaRect(c)
            
            M = cv2.moments(c)
            if M["m00"] != 0:
                cx_plat = int(M["m10"] / M["m00"])
                cy_plat = int(M["m01"] / M["m00"])
                centro_plataforma = (cx_plat, cy_plat)
 
    # ===============================================================
    # DETECCIÓN 2: PELOTA AZUL
    # ===============================================================
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    mask_pelota = cv2.inRange(hsv, LOW_BLUE, HIGH_BLUE)
    
    kernel_pelota = np.ones((5,5), np.uint8)
    mask_pelota = cv2.morphologyEx(mask_pelota, cv2.MORPH_OPEN, kernel_pelota)
    mask_pelota = cv2.morphologyEx(mask_pelota, cv2.MORPH_CLOSE, kernel_pelota)
    mask_pelota = cv2.dilate(mask_pelota, kernel_pelota, iterations=1)
    
    contours_pelota, _ = cv2.findContours(mask_pelota, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    area_max_pelota = 0
    contorno_pelota = None
    centro_pelota = None
    radio_pelota = 0
    
    for c in contours_pelota:
        area = cv2.contourArea(c)
        if area > area_max_pelota:
            area_max_pelota = area
            contorno_pelota = c
            (x_pel, y_pel), radio_pelota = cv2.minEnclosingCircle(c)
            if radio_pelota > RADIO_MIN_PELOTA and area > AREA_MIN_PELOTA:
                centro_pelota = (int(x_pel), int(y_pel))
    
    # ===============================================================
    # VISUALIZACIÓN
    # ===============================================================
    out_original = frame.copy()
    
    mask_combinada = cv2.bitwise_or(mask_plataforma, mask_pelota)
    out_deteccion = cv2.cvtColor(mask_combinada, cv2.COLOR_GRAY2BGR)
    
    out_deteccion[mask_plataforma > 0] = [255, 0, 0]
    out_deteccion[mask_pelota > 0] = [0, 255, 255]
 
    # ===============================================================
    # CALCULAR TIEMPO
    # ===============================================================
    current_time = time.time()
    dt = current_time - prev_time
    prev_time = current_time
    if dt < 0.001:
        dt = 0.001
 
    # ===============================================================
    # CONTROL PID - SOLO CAMBIO: ERROR X
    # ===============================================================
    plataforma_detectada = (contorno_plat is not None and area_max_plat > AREA_MIN_PLATAFORMA and centro_plataforma is not None)
    pelota_detectada = (contorno_pelota is not None and centro_pelota is not None)
    
    if plataforma_detectada:
        if rectangulo_info:
            box = cv2.boxPoints(rectangulo_info)
            box = np.intp(box)
            cv2.drawContours(out_original, [box], 0, (0, 255, 0), 3)
            cv2.drawCon
https://www.youtube.com/watch?v=BvbTCJa57hE
 
