# Constrained Device Application (Connected Devices)

## Lab Module 09

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

Se desarrolla un cliente CoAP pero ahora dentro del CDA para permitir la comunicación con el CoAP server del GDA. El cliente CoAP se utiliza para enviar y recibir datos entre el CDA y el GDA a través de solicitudes GET, PUT, POST, DELETE y OBSERVE:
  1.	Se configura el cliente CoAP con parámetros como el host y el puerto del servidor CoAP del GDA. El cliente se inicializa utilizando la biblioteca aiocoap y el módulo asyncio para manejar las solicitudes de forma asíncrona.
  2.	Métodos de Solicitud:
  - GET: El cliente solicita datos de un recurso específico en el GDA.
  - PUT: El cliente envía datos (como lecturas de sensores) al servidor.
  - POST y DELETE: Usados para crear o eliminar recursos, aunque no se detallan tanto en el capítulo.
  - OBSERVE: El cliente observa recursos del GDA para recibir actualizaciones automáticamente cuando hay cambios.
  4.	Las respuestas del servidor se procesan y se validan. Si algo falla, se maneja mediante excepciones.
  5.	Se utiliza asyncio para enviar solicitudes y esperar respuestas de forma asíncrona, lo que permite una comunicación eficiente sin bloquear el flujo principal.
  6.	Se usa Wireshark para capturar los paquetes CoAP y asegurar que la comunicación entre el CDA y el GDA sea la esperada.


How does your implementation work?

PIOT-CDA-09-001: Se crea el módulo CoapClientConnector que implementa IRequestResponseClient que funcionará como un cliente CoAP.
  -	En el constructor se configura la conexión al servidor CoAP usando las credenciales de comunicación y se inicializa el cliente CoAP.
  -	_initClient(): Se encarga de inicializar el cliente CoAP usando la librería aiocoap que crea un contexto de cliente de manera asíncrona.
  -	Se implementan los siguientes métodos pero de momento solo registramos un mensaje por consola: sendDiscoveryRequest(), sendGetRequest(), sendPostRequest(), sendPutRequest(), sendDeleteRequest(), startObserver(), stopObserver().
  -	_createResourcePath(): Forma la URL completa de un recurso CoAP que será utilizado en las solicitudes.
Finalmente, se integra este componente en el DeviceDataManager creando una instancia de esta clase.

PIOT-CDA-09-002: Se implementa soporte para solicitudes GET tanto confirmadas (CON) como no confirmadas (NON):
  -	sendGetRequest: Se encarga de emitir una solicitud GET al servidor CoAP. Dependiendo de si se pasa enableCON=True, la solicitud será confirmada o no confirmada.
  -	handleGetRequest: Maneja la creación y envío de la solicitud GET asíncrona, esperando la respuesta del servidor y luego pasando la respuesta a la función _onGetResponse para su posterior procesamiento.
  -	onGetResponse: Procesa la respuesta de la solicitud GET. Si la respuesta es válida, se extrae la carga útil en formato JSON y se maneja dependiendo del tipo de recurso.
  -	sendDiscoveryRequest: Esta función realiza una solicitud GET especial a la ruta /.well-known/core para descubrir los recursos disponibles en el servidor CoAP.

PIOT-CDA-09-003: En este caso se implementa soporte para solicitudes PUT tanto confirmadas como no confirmadas, siguiendo una estructura similar a la de la solicitud GET:
  -	sendPutRequest: Esta función es responsable de emitir la solicitud PUT al servidor CoAP. Si se pasa enableCON=True, la solicitud será confirmada, de lo contrario, será no confirmada.
  -	_handlePutRequest: Esta función maneja la creación y envío de la solicitud PUT, incluyendo la codificación del payload y el manejo asíncrono de la respuesta.
  -	_onPutResponse: Esta función maneja la respuesta a la solicitud PUT, procesando la carga útil y registrando la respuesta recibida.

PIOT-CDA-09-004: Ahora se agrega soporte para solicitudes POST al cliente CoAP, tanto confirmadas como no confirmadas:
  -	sendPostRequest: Esta función es la que se encarga de crear y enviar la solicitud POST al servidor CoAP. Utiliza el parámetro enableCON para decidir si la solicitud será confirmada o no confirmada.
  -	_handlePostRequest: Esta función maneja la construcción del mensaje POST, incluida la codificación del payload, y el envío de la solicitud al servidor CoAP. Luego, espera la respuesta.
  -	 _onPostResponse: Esta función procesa la respuesta recibida al hacer una solicitud POST y maneja el contenido de la carga útil.

PIOT-CDA-09-005: Se agrega soporte para manejar la respuesta a una solicitud DELETE (confirmada o no confirmada) enviada al servidor CoAP:
  -	sendDeleteRequest: Esta función se encarga de crear y enviar una solicitud DELETE al servidor CoAP. La solicitud puede ser confirmada o no confirmada según el parámetro enableCON. La función primero construye la ruta del recurso utilizando los parámetros resource y name, y luego invoca la función que maneja la solicitud DELETE de manera asincrónica (_handleDeleteRequest).
  -	_handleDeleteRequest: Esta función maneja la construcción del mensaje DELETE y el envío al servidor CoAP. Usa el parámetro enableCON para decidir si el mensaje será confirmado o no confirmado. Después de enviar la solicitud, la función espera la respuesta utilizando req.response y luego pasa la respuesta a la función _onDeleteResponse para su procesamiento.
  -	_onDeleteResponse: Esta función maneja la respuesta recibida después de enviar la solicitud DELETE. Si la respuesta es válida, la procesa y registra el contenido de la carga útil (payload) de la respuesta. Si la respuesta no es válida, muestra un mensaje de advertencia.

PIOT-CDA-09-006: Ahora en esta tarea se ha añadido soporte para solicitudes OBSERVE para permitir que el cliente CoAP observe cambios en recursos específicos en el servidor, procesando actualizaciones de esos recursos de manera continua:
  - startObserver: Esta función permite iniciar la observación de un recurso en el servidor CoAP. Envía una solicitud GET con la opción observe=0 para comenzar a recibir actualizaciones continuas sobre el recurso. Si el recurso ya está siendo observado, la solicitud es ignorada.
  - _handleStartObserveRequest: Esta función es responsable de enviar la solicitud de observación al servidor. Después de enviar la solicitud, espera y procesa las respuestas iniciales y las actualizaciones continuas.
  - stopObserver: Detiene la observación de un recurso específico. Si el recurso está siendo observado, cancela la observación y elimina la entrada correspondiente en el diccionario observeRequests. Si el recurso no está siendo observado, muestra una advertencia.
  - _handleStopObserveRequest: Se encarga de cancelar la observación de un recurso y eliminarlo del diccionario de solicitudes observadas. También maneja la cancelación de la observación activa, enviando una señal para dejar de recibir actualizaciones de ese recurso.
  - HandleActuatorEvent: Es una clase utilizada para gestionar las respuestas de los recursos observados. Procesa la carga útil de la respuesta (en caso de que el recurso sea un actuador), decodificándola en datos que luego son pasados a un listener (IDataMessageListener) para su manejo.
En cada actualización o respuesta del servidor, se verifica y maneja la información de manera apropiada. Si la carga útil contiene datos de actuador, se decodifican y se envían al listener para que este pueda realizar las acciones necesarias.

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule09 

### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- CoapClientConnectorTest

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

EOF.
