# Gateway Device Application (Connected Devices)

## Lab Module 10

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

En el GDA, se implementa una lógica complementaria. El cliente MQTT se actualiza para soportar TLS y credenciales, y se suscribe a los topics publicados por el CDA (sensores, rendimiento, respuestas de actuadores). Se crean manejadores específicos para procesar cada tipo de mensaje entrante. Por su parte, DeviceDataManager analiza los datos recibidos, por ejemplo, la humedad, y si detecta que está fuera del rango nominal durante un tiempo, genera un comando ActuatorData para ajustar el nivel de humedad. Ese comando se envía al CDA, y el GDA registra internamente qué comando se envió y cuándo, para evitar enviar órdenes innecesarias. Todo esto permite una comunicación segura, automatizada y bidireccional entre CDA y GDA.

How does your implementation work?

PIOT-GDA-10-001: En este caso se actualiza la clase MqttClientConnector del GDA para que soporte credenciales si se configuran y soporte conexiones seguras si se habilita:
  - Se implementa initCredentialConnectionParameters que carga usuario y contraseña del fichero de credenciales.
  - Se implementa initSecureConnectionParameters que carga el certificado TLS.
  - Se implementa initClientParameters, que lee todos los parámetros de conexión del archivo.
  - Se llama a initClientParameters desde el constructor.
    
PIOT-GDA-10-002: Se actualiza la clase MqttClientConnector del GDA para que se suscriba a los temas MQTT publicados por el CDA, concretamente: mensajes de SensorData, mensajes de SystemPerformanceData, mensajes de respuesta del ActuatorData y procese esos mensajes entrantes cuando lleguen al GDA:
  - Antes se usaba el cliente síncrono (MqttClient), pero puede causar bloqueos si se publica mientras recibe. Por eso, se ha cambiado por MqttAsyncClient.
  - Implementadas suscripciones a tres topics del CDA. Te suscribes a ellos en el método connectComplete para garantizar que solo te suscribes cuando ya hay conexión al broker.
  - Se implementan 3 clases internas que actúan como manejadores de mensajes MQTT. Estas clases implementan IMqttMessageListener y procesan los mensajes dependiendo del tipo:
    o	ActuatorResponseMessageListener: Convierte el JSON recibido a ActuatorData y lo pasa a IDataMessageListener.handleActuatorCommandResponse.
    o	SensorDataMessageListener: Convierte el JSON a SensorData y lo pasa a IDataMessageListener.handleSensorMessage.
    o	SystemPerformanceDataMessageListener: Convierte el JSON a SystemPerformanceData y lo pasa a IDataMessageListener.handleSystemPerformanceMessage.


PIOT-GDA-10-003: Aquí el objetivo es actualizar la clase DeviceDataManager del GDA para que procese correctamente los mensajes que llegan desde el CDA a través de MQTT (o CoAP), en concreto SensorData (por ejemplo, datos de humedad), SystemPerformanceData (datos sobre CPU, RAM…) y ActuatorData (respuestas del CDA a comandos previos):
  - Antes, los métodos handleSensorMessage, handleSystemPerformanceMessage y handleActuatorCommandResponse solo mostraban logs. Ahora, en handleSensorMessage se llama a un método privado.
  - Dentro de handleIncomingDataAnalysis se ha implementado el control del nivel de humedad recibido desde el CDA: Si la humedad está por debajo del mínimo o por encima del máximo, se espera un tiempo mínimo. Si la condición se mantiene, se genera un comando ActuatorData para encender o apagar el humidificador y se envía este comando al CDA usando MQTT (o CoAP, si estuviera activado).
  - Se ha añadido a la configuración variables de clase para recordar los últimos datos de humedad recibidos, guardar cuándo se recibió el último dato y saber si ya se había enviado un comando.
  - Se implementa el método sendActuatorCommandtoCda que llama al listener del actuador si existe y publica el comando MQTT en el topic CDA_ACTUATOR_CMD_RESOURCE.
  - Se implementa el método getDateTimeFromData que convierte timeStamp ISO 8601 en OffsetDateTime para calcular si han pasado suficientes segundos desde el último evento.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/java-components/tree/labmodule10

### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.


### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- MqttClientConnectorTest 
- DeviceDataManagerSimpleCdaActuationTest 

EOF.
