# Gateway Device Application (Connected Devices)

## Lab Module 11

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

A lo largo de estas tareas, se configuró el  GDA para conectarse de forma segura a la nube usando Ubidots STEM como proveedor MQTT con cifrado TLS y autenticación por token, almacenando estas credenciales en archivos específicos y actualizando el archivo PiotConfig.props. Se mejoró la clase MqttClientConnector para admitir tanto entornos locales como cloud, incorporando un ConnectionListener y métodos protegidos para facilitar la publicación y suscripción. Se creó la interfaz ICloudClient y se implementó CloudClientConnector, permitiendo enviar datos del GDA a la nube mediante MQTT y recibir eventos desde ella. Luego, se integró esta lógica en DeviceDataManager, de modo que el GDA recibe datos del CDA, los publica en Ubidots, y reacciona ante eventos remotos de actuación como led_actuator. Finalmente, se completó el flujo completo extremo a extremo en la tarea PIOT-GDA-11-004: el CDA publica datos al GDA, el GDA los reenvía a la nube, la nube evalúa reglas (si temperatura > 25, encender LED), publica un evento, el GDA lo recibe, crea un ActuatorData, lo reenvía al CDA, y este activa o desactiva el LED según corresponda.

How does your implementation work?

PIOT-GFG-11-001: En esta tarea configuramos el componente GDA para que se conecte a un servicio en la nube compatible con MQTT con cifrado TLS y autorización, usando Ubidots STEM como proveedor:
  - Crear una cuenta gratuita en Ubidots STEM.
  - Generar un token API personal, que usaremos para autorizar la conexión entre nuestro sistema y el broker MQTT de Ubidots.
  - Guardar ese token en un archivo de credenciales llamado UbidotsCloudCred.props.
  - Descargar el certificado raíz TLS de Ubidots, necesario para cifrar la conexión MQTT, y guardarlo como UbidotsCloudCert.pem.
  - Editar el archivo de configuración PiotConfig.props del GDA para incluir la ruta del archivo de credenciales, l ruta del certificado TLS, host, puerto seguro, base URL y tema base de Ubidots y activar el uso de cifrado.
    
PIOT-GDA-11-001: En esta tarea se ha actualizado la clase MqttClientConnector para hacerla más flexible y compatible con entornos locales y en la nube. Ahora la clase puede cargar configuración desde distintas secciones del archivo PiotConfig.props, y permite que otras clases del mismo paquete usen sus funciones de forma directa:
  - Variables nuevas:
    -	useCloudGatewayConfig: indica si se está usando una configuración cloud.
    -	connListener: permite notificar a otras clases cuando hay conexión.
  - Se añadieron 3 constructores para inicializar la clase según si se quiere usar configuración local o en la nube.
  - Se movió la lógica de configuración del constructor original a este método privado nitClientParameters para reutilizar el código.
  - El método setConnectionListener permite que otra clase sea notificada cuando se conecta el cliente MQTT.
  - Los métodos protegidos nuevos (publishMessage, subscribeToTopic y unsubscribeFromTopic) permiten que otras clases accedan directamente a las funciones MQTT usando nombres de temas personalizados.
  - Los métodos públicos existentes ahora delegan en los métodos protegidos para simplificar y evitar duplicación.
  - El método connectComplete() actualizado (Opción 2) se suscribe a los temas locales usando listeners personalizados solo si no se está usando configuración cloud.

PIOT-GDA-11-002: Se crea la interfaz ICloudClient para que las clases base implementen toda la lógica real de conexión con la nube garantizando que todas tengan los mismos métodos.

PIOT-GDA-11-003: Ahora se implementa la clase CloudClientConnector que usa MQTT para enviar datos del Gateway Device a la nube, integrándola en DeviceDataManager:
  - Crear clase CloudClientConnector: Implementa la interfaz ICloudClient, internamente usa MqttClientConnector para comunicarse con el broker en la nube. Se definen métodos sendEdgeDataToCloud para enviar SensorData y SystemPerformanceData.
  - Conectar a MQTT en la nube:
    -	El método connectClient instancia y conecta MqttClientConnector.
    -	El método disconnectClient desconecta el cliente si está conectado.
  - Publicar datos a la nube: Se construyen los topics con createTopicName, se publica usando publishMessageToCloud con MqttClientConnector y el envío de SensorData y SystemPerformanceData se realiza con métodos de ICloudClient.
  - Suscripciones desde la nube: Se implementan subscribeToCloudEvents y unsubscribeFromCloudEvents usando MqttClientConnector.
  - Integración en DeviceDataManager:
    -	Se crea una variable enableCloudClient.
    -	Se crea una instancia de ICloudClient cloudClient.
    -	Se conecta el cliente en startManager.
    -	Se desconecta en stopManager.
    Para enviar datos desde el GDA a la nube:
      -	En handleSensorMessage y handleSystemPerformanceMessage se llama al método handleUpstreamTransmission.
      -	Ese método usa cloudClient.sendEdgeDataToCloud para publicar los datos a la nube.

PIOT-GDA-11-004: La implementación de la tarea tiene como objetivo demostrar una comunicación completa de extremo a extremo entre el CDA, el GDA  y el servicio en la nube, permitiendo no solo la recolección de datos, sino también la actuación remota a través de eventos de control de LED.
  - En la nube creamos el device denominado constraineddevide y dentro de el añadimos la variable de temperatura (temperature) y el led (led_actuator).
  - CDA envía datos al GDA
    -	El CDA recoge datos como temperatura.
    -	Esos datos se encapsulan en objetos (SensorData, SystemPerformanceData) y se envían al GDA mediante MQTT.
  - GDA reenvía los datos a la nube
    -	En el GDA, la clase DeviceDataManager recibe los datos del CDA y los pasa al CloudClientConnector.
    -	CloudClientConnector utiliza MqttClientConnector para publicar los datos en los topics configurados del servicio cloud.
  - En la nube: se crea una regla de actuación:
    -	ALTA TEMPERATURA: si temperatura > 25 => led_actuator = 1
    -	BAJA TEMPERATURA: si temperatura <= 25 => led_actuator = 0
  - GDA se suscribe al topic de eventos LED
    -	En CloudClientConnector, se implementa onConnect (de IConnectionListener) para suscribirse automáticamente cuando se conecta al broker cloud.
    -	Se suscribe a led_actuator y se asocia un listener llamado LedEnablementMessageListener.
  - Este listener:
    -	Recibe el mensaje MQTT.
    -	Convierte el JSON en un objeto ActuatorData.
    -	Le añade nombre, tipo de actuado, ubicación, etc.
    -	Llama a DeviceDataManager.handleIncomingMessage y le pasa el JSON con el comando.
  - GDA reenvía el comando al CDA
    -	DeviceDataManager.handleIncomingMessage convierte el JSON a ActuatorData.
    -	Luego lo vuelve a convertir a JSON y lo publica en el topic MQTT del CDA.
  - CDA recibe el comando y actúa
    -	El CDA ya tiene lógica para escuchar en el topic MQTT.
    -	Cuando recibe el ActuatorData, se lo pasa a ActuatorAdapterManager.
    -	Este módulo interpreta si debe encender o apagar el LED.
    -	En el Sense HAT físico o emulado, se muestra el cambio.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/java-components/tree/labmodule11

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
- CloudClientConnectorTest

EOF.
