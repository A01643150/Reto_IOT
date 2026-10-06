Tecnológico de Monterrey
CAMPUS ESTADO DE MÉXICO
Implementación del internet de las cosas (IOT)
Cierre de ETAPA 1 del Reto
Sensores y componentes básicos
Fecha de entrega: 4 de OCT del 2026
Daniel Isaac Flores Cabanillas - A01643150
Emilio Xavier Sánchez Cerezo - A01805177

Arquitectura del sistema

• Capa de percepción:
Sensor de temperatura analógico LM35: Se eligió este sensor para medir la variable biomédica de temperatura corporal porque entrega 10 mV por cada grado centígrado y se conectará directamente al pin analógico A0 del microcontrolador.

• Capa de procesamiento:
Se selecciona el microprocesador ESP-32 porque no es un dispositivo restringido (cuenta con ~520 KB de SRAM y varios MB de flash), esto permite procesar los datos, hacer la conversión del voltaje a grados y correr el WiFi y MQTT sin optimizaciones extremas.

• Capa de red:
Conectividad vía WiFi (aprovechando el módulo del ESP-32) usando el estándar abierto MQTT y enviando los paquetes de datos en formato JSON

• Capa de Aplicación:
Plataforma open source Things Board para el broker y el dashboard, lo cual es ideal para prototipos sin costo de licencia y permite visualizar la temperatura del paciente en tiempo real.

• Capa de Negocio:
Sistema de notificaciones que alerta a familiares o médicos si la temperatura del paciente sale de los rangos seguros.

Render y Ubicación del Dispositivo

El dispositivo se construirá inicialmente como un prototipo de escritorio montado en una placa de pruebas (protoboard). El hardware principal (ESP-32) y el sensor de temperatura LM35 estarán interconectados de forma estática para garantizar la estabilidad de las conexiones eléctricas. Para adquirir la variable biomédica, el usuario colocará sus dedos directamente sobre el encapsulado del sensor LM35, transmitiendo así su temperatura corporal térmica. Este enfoque permite validar la arquitectura de red (WiFi/MQTT) y la visualización en la nube (ThingsBoard) sin los riesgos de desconexión por movimiento, dejando el encapsulado tipo "wearable" para una validación posterior con el profesor.


<img width="647" height="350" alt="Screenshot 2026-10-06 at 4 20 17 p m" src="https://github.com/user-attachments/assets/d0a570a1-924a-4155-91ba-74e45b6a6958" />

Prototipo inicial en placa de pruebas. El ESP-32 lee los datos analógicos del sensor LM35 estáticamente, permitiendo evaluar la conexión WiFi y el envío MQTT sin problemas de falso contacto.
