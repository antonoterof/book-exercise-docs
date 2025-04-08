# Gateway Device Application (Connected Devices)

## Lab Module 07

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

De la misma forma que en la práctica anterior, se configura y gestiona un cliente MQTT pero esta vez en el GDA, permitiendo que se conecte al broker, se suscriba a los temas relevantes (como datos de sensores y comandos de actuadores), publique mensajes y maneje la desconexión. Esto facilita la comunicación entre el GDA y el CDA, y permite la integración de servicios en la nube utilizando MQTT.

How does your implementation work?

PIOT-GDA-07-001: Creación de la clase MqttClientConnector que funciona como un intermediario entre el GDA y el broker MQTT:
  - Se implementa el constructor que inicializa las propiedades necesarias para la conexión MQTT. Se recuperan valores predeterminados de un archivo de configuración, como el host, puerto y tiempo de vida de la conexión. Se determina si se utilizará un cliente MQTT sincrónico o asincrónico basándose en la configuración.
  - Se implementan los métodos connectClient() y disconnectClient() que establecen la conexión con el cliente y la cierran respectivamente.

PIOT-GDA-07-002: Se agregan callbacks para manejar eventos de cliente MQTT:
  - Se implementa el método connectComplete() que se llamará cuando el cliente se haya conectado con éxito el broker y este registrará un mensaje.
  - Se implementa el método connectionLost(), que se llamará cuando el cliente pierda la conexión con el broker y este registrará un mensaje.
  - Se implementa el método deliveryComplete(), que se llamará cuando el cliente haya publicado un mensaje con éxito y registrará un mensaje indicando que se entregó correctamente.
  - Se implementa el método messageArrived(), que se llamará cuando el cliente reciba un mensaje en un tema al que esté suscrito y registra un mensaje también.

PIOT-GDA-07-003: Se implementa la capacidad de publicar mensajes en temas específicos, suscribirse a esos temas para recibir mensajes, y cancelar la suscripción cuando sea necesario:
  - publishMessage(): Gestiona la publicación de mensajes, recibe el nombre del tema donde se publicará el mensaje, el mensaje y el qos.
  - suscribeToTopic(): Gestiona la suscripción a un tema, recibe el nombre del tema al que suscribirse y el qos.
  - unsuscribeFromTopic(): Gestiona la cancelación de una suscripción a un tema, recibe el nombre del tema al que se quiere cancelar la suscripción.
  - isConnected(): Verifica si el cliente MQTT está actualmente conectado al broker, devolviendo true en caso afirmativo y false en caso contrario.

PIOT-GDA-07-004: Se integra la funcionalidad MQTT en la clase DeviceDataManager:
  - startManager(): Establece la conexión con el broker y se suscribe a los temas necesarios.
  - stopManager(): Detiene la conexión al manager y se desuscribe de todos los temas a los que se suscribió.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/java-components/tree/labmodule07


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
