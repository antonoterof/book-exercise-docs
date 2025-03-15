# Constrained Device Application (Connected Devices)

## Lab Module 05

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

La implementación de la CDA en este capítulo se centra en la recolección de datos del sistema y su conversión a un formato que pueda ser comprendido por otras aplicaciones, como la GDA. Aquí se destacan los siguientes pasos clave:
1.	Recolección de Datos de Desempeño del Sistema: Se captura información sobre la utilización de CPU y memoria. Se recopila estos datos a través de tareas asíncronas que obtienen la información sobre el uso del CPU y la memoria.
2.	Conversión de Datos a JSON: Se convierten las instancias de sensores a formato JSON para facilitar su transmisión y comprensión por otros sistemas. Se crea una función para convertir JSON de vuelta a objetos de datos.


How does your implementation work?

PIOT-CDA-05-001: Como ya comentamos, en SystemPerformanceManager se obtienen datos de la CPU y la memoria. Ahora lo que hicimos fue crear un objeto SystemPerformanceData con esos datos. Notificar a otros componentes del sistema enviando los datos a través del agente de escucha, si está configurado.

PIOT-CDA-05-002: Implementamos la clase DataUtil cuya función es convertir los datos (ActuatorData, SensorData y SystemPerformanceData) en formato JSON para que se puedan enviar entre diferentes componentes de la aplicación, en este caso al GDA. A su vez, convierte los datos en formato JSON de nuevo en objetos de Python, permitiendo que tu sistema lea y procese esos datos correctamente.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule05


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- DataUtilTest

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- SystemPerformanceManagerTest
- DataIntegrationTest

EOF.
