# Gateway Device Application (Connected Devices)

## Lab Module 05

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

La implementación del GDA en este capítulo se concentra en recibir y procesar los datos enviados por la CDA, además de convertirlos a un formato adecuado y almacenarlos si es necesario. Se incluyen:
1.	Gestión de los Contenedores de Datos: Al igual que en la CDA, en la GDA se crean contenedores para los datos para almacenar la información recibida. 
2.	Conversión de Datos a JSON: La GDA también convierte los contenedores de datos a JSON, similar a lo que se hizo en la CDA.
3.	Manejo de los Datos Recibidos: La GDA recibe y procesa los mensajes de datos de la CDA.


How does your implementation work?

PIOT-GDA-05-001: Creamos en el GDA las clases ActuatorData, SensorData y SystemPerformanceData:
- ActuatorData es un contenedor de datos que representa la información relacionada con un actuador.
- SensorData se encarga de almacenar la información relacionada con un sensor.
- SystemPerformanceData se encarga de almacenar la información relacionada con los recursos del sistema (memoria, cpu…).

PIOT-GDA-05-002: En esta tarea, implementamos funcionalidades dentro de la clase SystemPerformanceManager para manejar y almacenar los datos de rendimiento del sistema. Recopilamos datos de rendimiento cada vez que se llame al método handleTelemetry y llamamos al listener para manejar los datos de rendimiento y enviarlos.

PIOT-GDA-05-003: Implementamos de nuevo la clase DataUtil pero en este caso para el GDA. Como comenté anteriormente, esta clase se utiliza para convertir los objetos de sensores, actuadores y del sistema a formato json y así permitir que las aplicaciones se puedan comunicar.

PIOT-GDA-05-004: Creamos la clase DeviceDataManager encargada de manejar todo el procesamiento de datos dentro de la aplicación y ordena todas las solicitudes al destino apropiado. Gestiona las conexiones entre el dispositivo de puerta de enlace y otros dispositivos de la red. También recopila información del sistema y maneja los mensajes que llegan de sensores y actuadores, permitiendo que la aplicación de la puerta de enlace reciba y procese esos datos.

PIOT-GDA-05-005: Se integra el DeviceDataManager dentro de GatewayDeviceApp. De esta forma, la lógica de gestión de datos y rendimiento del sistema se delega a DeviceDataManager.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/java-components/tree/labmodule05


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- DataUtilTest
- ActuatorDataTest
- SensorDataTest
- SystemPerformanceDataTest
- SystemStateDataTest

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- SystemPerformanceManagerTest
- DataIntegrationTest
- GatewayDeviceAppTest


EOF.
