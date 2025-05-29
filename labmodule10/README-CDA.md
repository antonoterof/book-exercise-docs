# Constrained Device Application (Connected Devices)

## Lab Module 10

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 
En la implementación del CDA, se configura el cliente MQTT para soportar conexiones seguras mediante TLS. Esto implica cargar el certificado .pem si está habilitado, y conectar al puerto seguro del broker. Además, el CDA se suscribe automáticamente al topic de comandos de actuadores, y cuando recibe un mensaje en ese topic, lo convierte de JSON a un objeto ActuatorData y lo procesa. También se encarga de enviar datos al GDA (como lecturas de sensores o rendimiento del sistema) de forma automática, usando MQTT o CoAP. Si esos datos indican, por ejemplo, que la temperatura supera ciertos umbrales, el CDA lanza un evento de actuación que puede implicar encender o apagar un actuador.


How does your implementation work?

PIOT-CFG-10-001: Para empezar, se habilita TLS en la instancia local de Mosquitto para asegurar las conexiones MQTT entre el servidor y las aplicaciones cliente:
  -	Se instala el cliente de Mosquitto, aunque ya estaba instalado anteriormente.
  -	Se generan los certificados necesarios.
  -	Se actualiza la configuración de Mosquitto copiando los certificados a las rutas adecuadas.
  -	Se actualiza el archivo de configuración del GDA y CDA para usar el certificado server.crt.
  Esta actividad me ha llevado muchísimo tiempo porque no funcionaban por alguna razón y tuve que hacer virguerías.

PIOT-CDA-10-001: Se modifica la clase MqttClientConnector en el CDA para soportar conexiones seguras (TLS):
  - Se añaden dos propiedades en el constructor para leer si TLS está habilitado (enableCrypt) y para obtener la ruta del fichero .pem (certificado).
  - Se modifica el método connectClient() para usar puerto seguro si TLS está activado y cargar el certificado .pem usando ssl.PROTOCOL_TLS_CLIENT si enableEncryption es true.

PIOT-CDA-10-002: En esta tarea el objetivo es añadir soporte para recibir y procesar mensajes de tipo ActuatorData en el DeviceDataManager desde el GDA. En DeviceDataManager se implementa el método handleActuatorCommandMessage para verificar si data (el mensaje del actuador) es válido. Si lo es, lo procesa llamando a sendActuatorCommand() del componente actuatorAdapterMgr, que es quien emula o controla el actuador físico. Si no lo es, simplemente lo ignora y escribe un aviso en el log.

PIOT-CDA-10-003: Aquí lo que se hace es permitir que el cliente MQTT del CDA pueda suscribirse automáticamente al topic de comandos de actuadores, procesar los mensajes recibidos en ese topic y convertirlos en objetos ActuatorData. Hacer esto de forma asíncrona, sin bloquear el flujo del programa:
  - Se implementa un nuevo callback en MqttClientConnector llamado onActuatorCommandMessage, encargado de recibir los mensajes publicados en el topic de comandos de actuadores, convertir el mensaje JSON recibido en un objeto ActuatorData y enviarlo al listener configurado para que lo procese.
  - Se modifica el método onConnect para suscribirse al topic de comandos de actuadores justo después de establecer conexión con el broker y asociar el topic con el nuevo callback personalizado usando message_callback_add.
  - Se comenta la línea msgInfo.wait_for_publish() en el método publishMessage, para que las publicaciones sean no bloqueantes.

PIOT-CDA-10-004: Ahora el objetivo es que el CDA envíe datos al GDA automáticamente cuando recibe datos de sensores o rendimiento del sistema, los convierte a JSON y los transmite al GDA usando MQTT o CoAP. Detecta que la temperatura supera los umbrales configurados y se lanza un evento de actuación:
  - handleUpstreamTransmission( en DeviceDataManager es el encargado de enviar los datos al GDA.
  - Se llama a handleUpstreamTransmission desde handleSensorMessage. Este método se llama cuando el CDA recibe datos de sensores.
  - Se llama a handleUpstreamTransmission desde handleSystemPerformanceMessage. Este método se llama cuando se generan datos del rendimiento del sistema.
  - Ese método ya estaba implementado de antes. Se encarga de comparar la temperatura recibida con los límites y mandar un comando HVAC si se pasa de esos valores.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule10


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- MqttClientPerformanceTest
- MqttClientConnectorTest
- DeviceDataManagerCallbackTest
- DeviceDataManagerIntegrationTest

EOF.
