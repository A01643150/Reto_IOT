Tecnológico de Monterrey

Implementación del internet de las cosas (IOT)

Cierre de ETAPA 2 del Reto

Fecha de entrega: 5 de noviembre de 2026

Daniel Isaac Flores Cabanillas - A01643150

Emilio Xavier Sánchez Cerezo - A01805177

PARTE 1: Evidencia de lectura de sensores por serial

1. Conexiones de la prueba física del MPU6050
En la primera prueba, conectamos el MPU6050 al ESP32 para leer la aceleración, la velocidad angular y la temperatura por I²C. No usamos el DS18B20: la lectura de temperatura proviene del sensor interno del MPU6050 y corresponde al propio chip.
•  MPU6050: VCC → 3V3  |  GND → GND  |  SDA → GPIO 21  |  SCL → GPIO 22  |  AD0 → GND (0x68).


2. Fotografías del cableado físico
<img width="208" height="276" alt="Screenshot 2026-11-06 at 11 29 53 a m" src="https://github.com/user-attachments/assets/6152843e-d01d-4922-ace0-7f818c6e26b6" />

<img width="368" height="273" alt="Screenshot 2026-11-06 at 11 30 07 a m" src="https://github.com/user-attachments/assets/38a3e71a-9f7b-4bcb-809e-f410e870af7c" />

3. Código del sketch de prueba usado: MPU6050

#include <Wire.h>

const uint8_t MPU_ADDR = 0x68;

void setup() {

 Serial.begin(115200);
 
 Wire.begin(21, 22); // SDA, SCL
 
 delay(500);
  
  Comprobar identidad (WHO_AM_I)
    Wire.beginTransmission(MPU_ADDR);
    
    Wire.write(0x75);
    
    Wire.endTransmission(false);
    
    Wire.requestFrom(MPU_ADDR, (uint8_t)1);
    
    uint8_t id = Wire.read();
    
    Serial.printf("WHO_AM_I = 0x%02X (esperado 0x68)\n", id);
    
    // Despertar el sensor (sale de modo sleep)
    
    Wire.beginTransmission(MPU_ADDR);
    
    Wire.write(0x6B);
    
    Wire.write(0x00);
    
    Wire.endTransmission(true);
}

void loop() {

 Wire.beginTransmission(MPU_ADDR);
 
 Wire.write(0x3B); // primer registro del acelerómetro
 
 Wire.endTransmission(false);
 
 Wire.requestFrom(MPU_ADDR, (uint8_t)14);
 
    int16_t ax = (Wire.read() << 8) | Wire.read();
    
    int16_t ay = (Wire.read() << 8) | Wire.read();
    
    int16_t az = (Wire.read() << 8) | Wire.read();
    
    int16_t t = (Wire.read() << 8) | Wire.read();
    
    int16_t gx = (Wire.read() << 8) | Wire.read();
    
    int16_t gy = (Wire.read() << 8) | Wire.read();
    
    int16_t gz = (Wire.read() << 8) | Wire.read();
    
    Serial.printf("Acc[g]: %.2f %.2f %.2f | Gyro[°/s]: %.1f %.1f %.1f | Temp: %.1f °C\n",
    
             ax / 16384.0, ay / 16384.0, az / 16384.0,
             
             gx / 131.0, gy / 131.0, gz / 131.0,
             
             t / 340.0 + 36.53);
             
    delay(200);
    
}

4. Monitor serial: aceleración, giroscopio y temperatura
<img width="852" height="845" alt="Screenshot 2026-11-06 at 11 34 23 a m" src="https://github.com/user-attachments/assets/c90cee25-a319-452e-938d-72f0cae6cec0" />

PARTE 1.2: Prueba física del MAX30102

1. Conexiones del sensor óptico

•  MAX30102: VIN → 3V3  |  GND → GND  |  SDA → GPIO 21  |  SCL → GPIO 22. El pin INT quedó libre.

3. Fotografía del cableado físico

<img width="624" height="833" alt="Screenshot 2026-11-06 at 11 35 28 a m" src="https://github.com/user-attachments/assets/36dc34f3-f58c-4d69-ae3c-828028348a03" />

3. Sketch de referencia para leer los canales rojo e infrarrojo
Este sketch lee los dos canales ópticos. La captura de la página siguiente corresponde a otra prueba que muestra IR y BPM.

#include <Wire.h>

#include "MAX30105.h" // Librería compatible con MAX30102

MAX30105 particleSensor;

void setup() {

 Serial.begin(115200);
 
 Serial.println("Iniciando sensor MAX30102...");

    if (!particleSensor.begin(Wire, I2C_SPEED_FAST)) {
    
      Serial.println("No se encontró el MAX30102. Revisa el cableado.");
      
      while (1);
      
    }
    
    particleSensor.setup();
    
    particleSensor.setPulseAmplitudeRed(0x0A);
    
    particleSensor.setPulseAmplitudeIR(0x0A);
    
}

void loop() {

  long irValue = particleSensor.getIR();
  
  long redValue = particleSensor.getRed();
  
  Serial.print("Infrarrojo (IR): ");
  
  Serial.print(irValue);
  
  Serial.print(" | Rojo (RED): ");
  
  Serial.println(redValue);
  
  delay(100);
  
}

4. Monitor serial: lectura infrarroja y estimación de pulso
<img width="626" height="583" alt="Screenshot 2026-11-06 at 11 38 22 a m" src="https://github.com/user-attachments/assets/7b51ba3e-beed-4599-9707-1e5f69deced7" />

Observación sobre el ruido

En las diez líneas completas de la captura, el canal infrarrojo varió entre 84 090 y 84 380 cuentas: una diferencia de 290. La señal fluctuó alrededor de 84 mil cuentas. Con este registro no podemos separar la variación del pulso del ruido del sensor.

PARTE 1.3: Diagrama de conexiones unificado
<img width="599" height="435" alt="Screenshot 2026-11-06 at 11 39 22 a m" src="https://github.com/user-attachments/assets/216370f7-897f-4da0-bea9-3e615d1d456a" />

El MAX30102 (0x57) y el MPU6050 (0x68) comparten alimentación de 3.3 V, tierra, SDA (GPIO 21) y SCL (GPIO 22). El diagrama muestra los dos módulos del montaje.

Lectura conjunta: IR, rojo y temperatura interna del MPU6050

Prueba pendiente de documentar. El siguiente sketch reúne las tres lecturas en una línea del monitor serial a 115200 baudios.
<img width="463" height="366" alt="Screenshot 2026-11-06 at 11 40 51 a m" src="https://github.com/user-attachments/assets/44602024-baa4-4838-9ef7-36069b174268" />

Sketch para la prueba conjunta

Librería del MAX30102: SparkFun MAX3010x Pulse and Proximity Sensor Library. El MPU6050 se consulta con Wire.

#include <Wire.h>

#include "MAX30105.h"

MAX30105 optico;

const uint8_t MPU = 0x68;

void setup() {

  Serial.begin(115200);
  
  Wire.begin(21, 22);
  
  if (!optico.begin(Wire, I2C_SPEED_STANDARD)) {
  
    Serial.println("MAX30102 no detectado");
    
    while (true) delay(1000);
    
  }
  optico.setup(0x0A, 4, 2, 100, 411, 4096); // Modo rojo + IR
  
  Wire.beginTransmission(MPU);
  
  Wire.write(0x6B);
  
  Wire.write(0); // Despertar el MPU6050
  
  if (Wire.endTransmission() != 0) {
  
    Serial.println("MPU6050 no detectado");
    
    while (true) delay(1000);
    
  }
  
}

void loop() {

  optico.check();
  
  if (!optico.available()) return;
  
  uint32_t rojo = optico.getFIFORed();
  
  uint32_t ir = optico.getFIFOIR();
  
  optico.nextSample();

  Wire.beginTransmission(MPU);
  
  Wire.write(0x41); // TEMP_OUT_H
  
  if (Wire.endTransmission(false) != 0) return;
  
  if (Wire.requestFrom(MPU, (uint8_t)2) != 2) return;
  
  uint8_t alto = Wire.read();
  
  uint8_t bajo = Wire.read();
  
  int16_t crudo = (int16_t)((alto << 8) | bajo);
  
  float temp = crudo / 340.0f + 36.53f;
  
  Serial.printf("IR: %lu | Rojo: %lu | Temp. MPU: %.2f °C\n",
  
                 (unsigned long)ir, (unsigned long)rojo, temp);
}

5. Preguntas de reflexión

•  ¿Por qué se necesitan luz roja e infrarroja para estimar la SpO₂?

La hemoglobina oxigenada y la desoxigenada absorben de forma distinta el rojo y el infrarrojo. Comparar cómo cambia cada señal con el pulso permite estimar la saturación. Un solo canal permite detectar latidos, pero no distinguir por sí solo ambas contribuciones.

•  Entre el DS18B20 y el MLX90614, ¿cuál elegiríamos para la muñeca?

Elegiríamos el DS18B20 para una versión con medición por contacto. Una pulsera puede mantenerlo apoyado en la piel; con el MLX90614 tendríamos que controlar la distancia y la orientación al mover la muñeca. En esta etapa no instalamos el DS18B20. La temperatura que aparece en la prueba es la interna del MPU6050.

•  ¿Qué otro actuador serviría para emitir una alerta?

Para una persona con discapacidad visual usaríamos un zumbador piezoeléctrico. Dos pitidos cortos podrían indicar un aviso y una secuencia repetida, una alerta que requiere atención.

PARTE 2: Justificación y evidencias de conexión Wi-Fi del ESP32

1. Wi-Fi de 2.4 GHz (802.11 b/g/n)

Elegimos Wi-Fi porque el ESP32 puede conectarse al router de la casa sin depender de un teléfono como intermediario. BLE sería útil si la pulsera enviara los datos al celular; LoRaWAN tendría sentido para distancias mayores, pero requiere cobertura de una pasarela. Para esta prueba basta la red local de 2.4 GHz del ESP32 clásico; no utilizamos 802.11ac.

2. Conexión y dirección IP
<img width="1044" height="780" alt="Screenshot 2026-11-06 at 11 45 52 a m" src="https://github.com/user-attachments/assets/70a80d64-aaf4-4632-a15a-aeec4109f63c" />

3. Prueba de conectividad
<img width="639" height="322" alt="Screenshot 2026-11-06 at 11 46 23 a m" src="https://github.com/user-attachments/assets/97823bb6-bb6d-4f86-9e92-03f8312bde5d" />
