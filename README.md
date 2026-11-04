Tecnológico de Monterrey

CAMPUS ESTADO DE MÉXICO

Implementación del internet de las cosas (IOT)
Cierre de ETAPA 1 del Reto

Sensores y componentes básicos

Fecha de entrega: 4 de OCT del 2026

Daniel Isaac Flores Cabanillas - A01643150

Emilio Xavier Sánchez Cerezo - A01805177


Capa de percepción: Sensor de temperatura digital DS18B20: Sonda con cable y punta de acero inoxidable elegida por su resistencia al contacto directo con la piel y el sudor. Entrega la temperatura digitalmente por 1-Wire (el pin DQ va al GPIO4, con una resistencia pull-up de 4.7 kΩ conectada entre DQ y 3.3 V). La variable medida es la temperatura superficial de contacto.

Sensor óptico MAX30102 (Predeterminado): Se integrará para medir la oximetría (SpO2 estimada) y la frecuencia cardíaca estimada, cubriendo así las variables vitales predeterminadas requeridas para el monitoreo.
Acelerómetro MPU6050 (Variable extra): Se utilizará para medir los niveles de actividad e inactividad física del usuario. (Nota: La detección de caídas se considera una mejora para el desarrollo futuro).

Capa de procesamiento:
Se selecciona el microcontrolador ESP-32 porque no es un dispositivo restringido (cuenta con ~520 KB de SRAM y varios MB de flash). Esto permite procesar los datos de los múltiples sensores simultáneamente (recibiendo señales I2C y 1-Wire), empaquetar toda la información en formato JSON y correr la conectividad WiFi y MQTT sin optimizaciones extremas.

Capa de red:

Conectividad vía WiFi (aprovechando el módulo del ESP-32) usando el estándar abierto MQTT y enviando los paquetes de datos (temperatura, SpO2, ritmo cardíaco, actividad e inactividad) de manera continua hacia la plataforma en formato JSON.

Capa de Aplicación:Plataforma open source ThingsBoard CE desplegada en red local para el broker (MQTT) y el dashboard, respaldada por una base de datos PostgreSQL. Este dashboard incluirá la visualización y análisis de 5 indicadores exactos: temperatura superficial, SpO₂ estimada, frecuencia cardíaca estimada, actividad, e inactividad.

Capa de Negocio: Sistema de alertas locales mediante un LED en el dispositivo y avisos visuales en el dashboard para que el cuidador pueda revisarlos.
Render y Ubicación del Dispositivo

El dispositivo se construirá inicialmente como un prototipo de escritorio montado en una placa de pruebas (protoboard) para las primeras pruebas de conexión. El hardware principal (ESP-32) y el sensor de temperatura DS18B20 estarán interconectados de forma estática para garantizar la estabilidad de las conexiones eléctricas. Para adquirir la variable biomédica, el usuario hará contacto con el encapsulado de acero del sensor DS18B20 transmitiendo así su temperatura superficial. Para la demostración final del wearable, conservaremos una sujeción básica y la alimentación portátil por USB.

<img width="1024" height="572" alt="image" src="https://github.com/user-attachments/assets/c11a0407-c88a-4850-b8c0-e83cd15a7339" />


En esta representación se incluye:
El ESP-32 como el cerebro central, alimentado por USB.

El DS18B20 (sensor con cable negro y punta de acero) para la temperatura, integrando su resistencia de 4.7 kΩ.

El MAX30102 (módulo rojizo/morado) para medir la oximetría y la frecuencia cardíaca.

El MPU6050 (módulo azul) es la variable extra elegida para detectar actividad e inactividad.

Todos los sensores están conectados a la protoboard compartiendo el riel de alimentación de 3.3 V y una tierra común (GND). Se utilizan los canales de comunicación digital 1-Wire (para el DS18B20) e I2C (para el MAX30102 y el MPU6050).

LINK DEL REPOSITORIO (Comprobado como público): https://github.com/A01643150/Reto_IOT


Todos están conectados en la protoboard, compartiendo pines de alimentación (3.3 V / 5 V) y los canales de comunicación analógica (para el DS18B20) y de I2C (para el MAX30102 y el MPU6050).

