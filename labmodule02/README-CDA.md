# Constrained Device Application (Connected Devices)

## Lab Module 02

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do?

En este capítulo se comienzan a implementar dos aplicaciones que se comunicarán entre sí y con un sistema centralizado, como un servidor o la nube. La implementación en el CDA se centró en la recopilación y gestión de datos del sistema. El principal objetivo era gestionar el rendimiento del sistema de la aplicación en un dispositivo limitado, con funciones clave como:
  
  - Recopilación de datos de rendimiento: Se recolecta información sobre el rendimiento del sistema, como el uso de CPU y la memoria utilizando tareas específicas.
  
  - Monitoreo y gestión de tareas: Estas tareas son ejecutadas de forma asíncrona y periódica para actualizar la     información de rendimiento del sistema, como el uso de la CPU y la memoria. La información es recopilada y registrada   para el análisis posterior.


How does your implementation work?

PIOT-CDA-02-001: Se crea el módulo ConstrainedDeviceApp, y definimos una clase llamada ConstrainedDeviceApp. Esta clase contiene métodos clave que gestionan el ciclo de vida de la aplicación:
•	startApp(): Este método se encarga de iniciar la aplicación. Lo que hace es registrar un mensaje de información en los logs, indicando que la aplicación ha comenzado a ejecutarse.
•	stopApp(): Este método detiene la aplicación, y también registra un mensaje de información en los logs, indicando que la aplicación ha sido detenida.
•	main(): La función principal de entrada que se encarga de crear una instancia de la clase ConstrainedDeviceApp, llama a startApp(), espera 65 segundos (simulando el tiempo de ejecución de la aplicación), y luego llama a stopApp().
PIOT-CDA-02-002: Creamos la clase SystemPerformanceManager que gestiona el rendimiento del sistema, configurando y controlando parámetros como el ciclo de sondeo y la ubicación del dispositivo, que se leen desde un archivo de configuración.
PIOT-CDA-02-003: Se integra el SystemPerformanceManager dentro de la aplicación ConstrainedDeviceApp para gestionar el rendimiento del sistema.
PIOT-CDA-02-004: Se crea la clase BaseSystemUtilTask que proporciona una estructura base para gestionar tareas relacionadas con el sistema, en la que se definen propiedades comunes y métodos que luego serán utilizados y extendidos por clases derivadas.
PIOT-CDA-02-005: Se crea la clase SystemCpuUtilTask que extiende la clase e implementa el método plantilla getTelemetryValue() que obtiene el porcentaje de utilización de la CPU en el momento actual y lo devuelve como un valor flotante.
PIOT-CDA-02-006: De nuevo, creamos otro módulo denominado SystemMemUtilTask que también extiende BaseSystemUtilTask y se encarga de obtener el porcentaje de memoria del sistema usada.
PIOT-CDA-02-007: Se integran las clases SystemCpuUtilTask y SystemMemUtilTask dentro del SystemPerformanceManager convirtiéndose este en el encargado de monitorear la cpu y la memoria del sistema.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule02

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

- ConstrainedDeviceAppTest
- SystemPerformanceManagerTest


EOF.
