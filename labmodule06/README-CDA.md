# Constrained Device Application (Connected Devices)

## Lab Module 06

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

El capítulo 6 se centra en la integración de MQTT en el CDA y dice cómo construir un cliente de tipo Publish/Subscribe MQTT. Para ello creo la clase MqttClientConnector, que funciona como un adaptador que se encarga de delegar las llamadas a la instancia subyacente del cliente MQTT. Conecta el CDA al broker MQTT.

How does your implementation work?

PIOT-CFG-06-001: Instalación del servidor Mosquitto.

PIOT-CDA-06-001: Creación de la clase MqttClientConnector. 
  - En el constructor se carga la configuración, se obtiene información como la dirección del host del broker, el puerto, y los valores relacionados con la conexión. Establece un clientID único: Si no se proporciona un clientID, se obtiene de la configuración. Configura los valores de la conexión MQTT, como el tiempo de keepAlive.
  - Se implementa el método connectClient() para conectar el cliente MQTT al broker, asocia las funciones de callback (métodos que se ejecutan automáticamente cuando ocurren evento) y el cliente empieza a escuchar los mensajes en segundo plano.
  - Se implementa el método disconnectClient() para desconectar al cliente MQTT del broker cuando ya no sea necesario.

PIOT-CDA-06-002: Se agregan callbacks en el módulo MqttClientConnector para manejar los eventos del cliente MQTT:
  - onConnect(): Este callback se ejecuta cuando el cliente MQTT se conecta al broker.
  - onDisconnect(): Este callback se ejecuta cuando el cliente MQTT se desconecta del broker. 
  - onMessage(): Este callback se activa cada vez que el cliente recibe un mensaje en un tema al que       está suscrito. El objetivo es registrar el mensaje recibido y su contenido.
  - onPublish(): Este callback se activa cuando el cliente publica un mensaje. 
  - onSubscribe(): Este callback se activa cuando el cliente se suscribe a un tema.

PIOT-CDA-06-003: Se agregan capacidades de publicación, suscripción y desuscripción a la clase MqttClientConnector para permitir que el cliente MQTT pueda publicar mensajes a un tema y suscribirse a un tema en el broker MQTT.
  - publishMessage(): Gestiona la publicación de mensajes, recibe el nombre del tema donde se publicará el mensaje, el mensaje y el qos.
  - suscribeToTopic(): Gestiona la suscripción a un tema, recibe el nombre del tema al que suscribirse, la función que se llamará cuando se reciban mensajes en el tema y el qos.
  - unsuscribeFromTopic(): Gestiona la cancelación de una suscripción a un tema, recibe el nombre del tema al que se quiere cancelar la suscripción.

PIOT-CDA-06-004: Se integra el cliente MqttClientConnector en el DeviceDataManager para que el cliente se conecte y se desconecte del broker MQTT al iniciar y detener el DeviceDataManager.



### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule06

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

EOF.
