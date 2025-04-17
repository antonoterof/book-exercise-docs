# Gateway Device Application (Connected Devices)

## Lab Module 08

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

En este capítulo pasamos a implementar el protocolo CoAP en el GDA, es decir, se crea un servidor de comunicaciones solicitud/respuesta que maneja las solicitudes de los CDA para recursos específicos a través de métodos como GET, PUT, POST y DELETE. Utiliza una estructura jerárquica para los nombres de los recursos y gestiona las solicitudes de manera asíncrona para garantizar la eficiencia:
  1.	Manejo de recursos: El servidor gestiona estos recursos mediante resource handlers que responden a solicitudes GET para obtener datos, PUT para actualizar datos, POST para crear o modificar recursos, y DELETE para eliminar recursos.
  2.	Asincronía y confirmación de mensajes: Se utilizan mensajes confirmados (CON) y no confirmados (NON) para garantizar la fiabilidad de las comunicaciones, y gestiona retransmisiones y la detección de duplicados.
  3.	Observación de recursos: Se implementa la funcionalidad de "observabilidad" que permite a los clientes suscribirse a los cambios de un recurso y ser notificados cuando este se actualiza, sin necesidad de realizar solicitudes constantes.

En conclusión, nos encargamos de que el GDA pueda recibir datos del cliente, los procese correctamente y permitimos que los clientes soliciten esos datos o realicen cambio usando como protocolo CoAP.


How does your implementation work?

PIOT-CFG-08-001: Se instaló y configuró el conjunto de herramientas Californium CoAP para probar el servidor CoAP. Posteriormente, para probar su correcto funcionamiento, se inició el servidor CoAP y se ejecutó un cliente para enviar una solicitud GET al servidor.

PIOT-CFG-08-002: Instalación de aiocoap, librería de CoAP.

PIOT-GDA-08-001: Se crea la clase CoapServerGateway que actuará como el servidor CoAP. De momento se implementan los métodos:
- startServer(): Inicia el servidor.
- stopServer(): Detiene el servidor.
  
Por otro lado, como ya se hizo con MQTT, integramos en la clase DataDeviceManager el servidor de forma que se delega el manejo de los mensajes de datos al IDataMessageListener que se pasa al servidor CoAP:

- startManager(): inicia el servidor.
- stopManager(): detiene el servidor.
  
PIOT-GDA-08-002: Se crean dos clases que actúan como controladores de recursos para manejar las solicitudes PUT de SensorData y SystemPerformanceData. Estas clases estarán modeladas a partir de la clase genérica GenericCoapResourceHandler. Se implementa el método handlePUT(), que recibe los datos (en formato JSON) enviados en la solicitud PUT, procesarlos (por ejemplo, convertirlos a objetos Java) y luego realizar la acción correspondiente. Se definen los métodos handleGET(), handlePOST() y handleDELETE().

PIOT-GDA-08-003: Se crea la clase GetActuatorCommandResourceHandler que extiende CoapResource e implementa IActuatorDataListener. Se defibe un constructor que recibe el nombre del recurso y lo pasa al constructor de la superclase. El recurso debe se configura como observable, lo que permite que el GDA notifique a los clientes sobre actualizaciones de datos del actuador. Se implementa el método onActuatorDataUpdate que se ejecuta cuando hay una actualización de datos del actuador, lo que implica almacenar los nuevos datos y notificar a los clientes observadores. Se implementa el método handleGET que acepta la solicitud, convierte los datos del actuador a formato JSON, y luego envía una respuesta con el código de respuesta adecuado.

PIOT-GDA-08-004: En DeviceDataManager se crea el método setActuatorDataListener para permitir la asignación de un oyente que manejará los datos de actuadores. Se actualiza CoapServerGateway:
- initServer(): Crea la instancia del servidor CoAP y llamar a initDefaultResources que inicializa los recursos predeterminados como GetActuatorCommandResourceHandler y otros controladores necesarios.
- addResource(): Permite agregar controladores de recursos al servidor CoAP. Estos controladores pueden ser tanto internos (por ejemplo, instanciados en CoapServerGateway) como externos (pasados desde DeviceDataManager).
- createAndAddResourceChain(): Permite crear una cadena de recursos basada en un nombre jerárquico, añadiendo los controladores necesarios en cada nivel.
- initDefaultResources(): Inicializa los recursos predeterminados del servidor CoAP, como GetActuatorCommandResourceHandler, UpdateTelemetryResourceHandler, y UpdateSystemPerformanceResourceHandler.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/java-components/tree/labmodule08

### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- CoapClientToServerConnectorTest

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

EOF.
