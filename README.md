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

Sensor de temperatura analógico LM35: Se eligió este sensor para medir la variable biomédica de la temperatura corporal porque entrega 10 mV por cada grado centígrado y se conectará directamente al pin analógico A0 del microcontrolador.

Sensor óptico MAX30102 (Predeterminado): Se integrará para medir la saturación de oxígeno (SpO2) y la frecuencia cardíaca, cubriendo así las variables vitales predeterminadas requeridas para el monitoreo.

Acelerómetro MPU6050 (Variable extra): Se utilizará para la detección de caídas repentinas, una variable elegida por el equipo que aporta un valor real y crítico para el cuidado de personas vulnerables y de la tercera edad.

• Capa de procesamiento:

Se selecciona el microcontrolador ESP-32 porque no es un dispositivo restringido (cuenta con ~520 KB de SRAM y varios MB de flash). Esto permite procesar los datos de múltiples sensores simultáneamente, convertir el voltaje a grados, empaquetar toda la información en formato JSON y ejecutar la conectividad WiFi y MQTT sin optimizaciones extremas.

• Capa de red:

Conectividad vía WiFi (aprovechando el módulo del ESP-32) usando el estándar abierto MQTT y enviando los paquetes de datos (temperatura, SpO2, ritmo cardiaco y movimiento) de manera continua hacia la nube en formato JSON.

• Capa de Aplicación:

Plataforma open source ThingsBoard para el broker y el dashboard, lo cual es ideal para prototipos sin costo de licencia. Este dashboard incluirá la visualización y análisis de un mínimo de 5 indicadores, permitiendo visualizar la temperatura del paciente, sus niveles de oxigenación, su frecuencia cardiaca y alertas de actividad/caídas en tiempo real.

• Capa de Negocio:

Sistema de notificaciones que alerta de forma inmediata a familiares o médicos ante eventos de riesgo; por ejemplo, si la temperatura del paciente sale de los rangos seguros, si presenta baja oxigenación o taquicardias, o si el acelerómetro detecta un impacto que sugiera una caída.
Render y Ubicación del Dispositivo

• Render y ubicación del dispositivo:

El dispositivo se construirá inicialmente como un prototipo de escritorio montado en una placa de pruebas (protoboard). El hardware principal (ESP-32) y el sensor de temperatura LM35 estarán interconectados de forma estática para garantizar la estabilidad de las conexiones eléctricas. Para adquirir la variable biomédica, el usuario colocará sus dedos directamente sobre el encapsulado del sensor LM35, transmitiendo así su temperatura corporal térmica. Este enfoque permite validar la arquitectura de red (WiFi/MQTT) y la visualización en la nube (ThingsBoard) sin los riesgos de desconexión por movimiento, dejando el encapsulado tipo "wearable" para una validación posterior con el profesor.


<img width="1024" height="564" alt="image" src="https://github.com/user-attachments/assets/71c3384d-13ff-43b1-a8d5-c9d880cd1453" />

En esta representación se incluye:
El ESP-32 es el cerebro central recibiendo la alimentación por USB.
El LM35 (componente negro de 3 pines) para la temperatura.
El MAX30102 (módulo rojizo/morado) para medir la saturación de oxígeno (SpO2) y la frecuencia cardíaca.
El MPU6050 (módulo azul) es la variable extra elegida para detectar las caídas.
Todos conectados en la protoboard compartiendo pines de alimentación (3.3 V / 5 V) y los canales de comunicación analógica (para el LM35) y de I2C (para el MAX30102 y el MPU6050).

