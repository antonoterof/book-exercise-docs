# Gateway Device Application (Connected Devices)

## Lab Module 02

Be sure to implement all the PIOT-GDA-* issues.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

En este capítulo se comienzan a implementar dos aplicaciones que se comunicarán entre sí y con un sistema centralizado, como un servidor o la nube. LA implementación en el GDA se centró en gestionar la recepción y el procesamiento de los datos provenientes del CDA, y en algunos casos, en generar su propio telemetría del sistema. Las funciones clave en el GDA incluyeron:

  - Recepción de datos: Recibir los datos generados por el CDA. Esto incluye datos de rendimiento como el uso de CPU y memoria.
  
  - Monitoreo del sistema: Similar al CDA, el GDA  también maneja las métricas de rendimiento del sistema (CPU y memoria). El GDA monitorea su propio rendimiento, pero su principal tarea es recibir y procesar los datos del CDA.


How does your implementation work?

PIOT-GDA-02-001: Se crea la clase GatewayDeviceApp que contiene la lógica de la aplicación. Contiene métodos para iniciar, detener y configurar la aplicación.

PIOT-GDA-02-002: Creamos la clase SystemPerformanceManager que de igual forma que el CDA, en el GDA gestiona el rendimiento del sistema, configurando parámetros como el ciclo de sondeo. Por ahora solo creamos dos funciones de start y stop de este.

PIOT-GDA-02-003: Importamos el SystemPerformanceManager en el GatewayDeviceApp de forma que ahora este módulo gestionará su funcionamiento mendiante los métodos startApp y stopApp que iniciará, y pararán el SystemPerformanceManager respectivamente.

PIOT-GDA-02-004: Ahora se crea la clase BaseSystemUtilTask que proporciona una estructura base para gestionar tareas relacionadas con el sistema, en la que se definen propiedades comunes y métodos que luego serán utilizados y extendidos por clases derivadas. 

PIOT-GDA-02-005: Se crea la clase SystemCpuUtilTask que extiende la clase e implementa el método plantilla getTelemetryValue() que obtiene el porcentaje de utilización de la CPU en el momento actual y lo devuelve como un valor flotante.

PIOT-GDA-02-006: De nuevo, creamos otro módulo denominado SystemMemUtilTask que también extiende BaseSystemUtilTask y se encarga de obtener el porcentaje de memoria del sistema usada.

PIOT-GDA-02-007: Se vuelven a integrar las clases SystemCpuUtilTask y SystemMemUtilTask dentro del SystemPerformanceManager convirtiéndose este en el encargado de monitorear la cpu y la memoria del sistema.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/java-components/tree/labmodule02


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ConfigUtilTest
- SystemCpuUtilTaskTest
- SystemMemUtilTaskTest


### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- SystemPerformanceManagerTest
- GatewayDeviceAppTest


EOF.
