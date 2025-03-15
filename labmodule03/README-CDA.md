# Constrained Device Application (Connected Devices)

## Lab Module 03

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

Generación de Datos de Sensores Simulados: El código emula la funcionalidad de los sensores, que serían dispositivos físicos en un entorno real. Los sensores, como los de temperatura, presión y humedad, generan datos que imitan las lecturas que estos dispositivos reales proporcionarían.
Para esto, se usa una clase denominada SensorDataGenerator, que simula el comportamiento de los sensores generando datos dentro de un rango definido. Estos valores generados son representaciones de las lecturas que un sensor real podría proporcionar en un entorno físico.
Cada sensor tiene configuraciones como el rango de valores y su comportamiento está basado en umbral y límites configurables que se encuentran en los archivos de configuración.

Simulación de Actuadores: Se emula el comportamiento de actuadores con la clase BaseActuatorSimTask y sus derivados como HvacActuatorSimTask y HumidifierActuatorSimTask. Estos actuadores están programados para reaccionar a los datos de los sensores.

Interacción entre Sensores y Actuadores: La implementación incluye un módulo de orquestación que controla cómo los sensores y actuadores interactúan entre sí. El SensorAdapterManager es el encargado de gestionar la recolección periódica de datos de los sensores simulados. Cada sensor está asociado a una tarea de simulación que, cuando se ejecuta, genera nuevos datos. El SensorAdapterManager organiza la recolección de estos datos en intervalos de tiempo definidos y los pasa a las partes relevantes del sistema para su procesamiento.
Los datos recopilados por los sensores se analizan para verificar si alguno de los valores cruza un umbral. Si esto ocurre, el sistema activa un actuador apropiado. Los comandos de los actuadores son enviados a través del ActuatorAdapterManager, que gestiona la activación y desactivación de los actuadores en función de las condiciones del sistema.

Orquestación y Control: Finalmente, todo el sistema de simulación es gestionado por el DeviceDataManager, que coordina los distintos componentes del sistema: la recolección de datos de los sensores, el procesamiento de esos datos para detectar cambios relevantes y el envío de comandos a los actuadores. Este administrador se asegura de que, cuando un sensor detecta un cambio significativo, el actuador adecuado se active o desactive para ajustarse a la nueva condición.


How does your implementation work?

PIOT-CDA-04-001: SensorData, que extiende de BaseIotData, está diseñada para representar datos de sensores. Configura el tipo de sensor, su nombre y valor del sensor y actualiza su valor si los datos proporcionados son del tipo adecuado. ActuatorData, que también extiende de BaseIotData, está diseñada para representar los datos de un actuador. Configura el tipo de actuador, nombre, valor, comando (acción a realizar), datos de estado y un indicador de respuesta. Además, actualiza los valores de comando, datos de estado, valor e indicador de respuesta si los datos proporcionados son del tipo adecuado. SystemPerformanceData almacena y actualiza datos sobre el rendimiento de un sistema (uso de CPU y memoria).

PIOT-CDA-04-002: BaseSensorSimTask se usa para la generación de datos de sensores, ya sea generando valores aleatorios o utilizando un conjunto de datos predefinido. Crea una instancia de datos de sensor, que usa para almacenar los datos de simulación de sensor más recientes, y proporciona una interfaz pública para generar una nueva instancia y acceder a sus datos.

PIOT-CDA-04-003: Las clases HumiditySensorSimTask, PressureSensorSimTask y TemperatureSensorSimTask extienden de la clase base BaseSensorSimTask y cada una simula la generación de datos de sensores específicos (humedad, presión y temperatura respectivamente).

PIOT-CDA-04-004: La clase BaseActuatorSimTask simula la activación y desactivación de un actuador. Cuando recibe un comando, procesa si debe activar o desactivar el actuador según el comando recibido (ON/OFF). Proporciona una funcionalidad base para manejar actuadores. Define métodos comunes como activar y desactivar un actuador mediante el método updateActuator().

PIOT-CDA-04-005: Las clases HumidityActuatorSimTask y HvacActuatorSimTask heredan de BaseActuatorSimTask y simulan el comportamiento de dos actuadores específicos (un humidificador y un sistema HVAC (calefacción, aire acondicionado y ventilación) respectivamente).

PIOT-CDA-04-006: La clase SensorAdapterManager se encarga de coordinar la simulación y la recolección periódica de datos de los sensores (humedad, presión, temperatura). Su función principal es:
  1.	Generar datos simulados de sensores.
  2.	Recolección periódica de esos datos a intervalos regulares.
  3.	Enviar los datos generados a otros componentes para su procesamiento.

PIOT-CDA-04-007: La clase ActuatorAdapterManager se encarga de coordinar los actuadores. Recibe comandos (como encender o apagar un actuador) y ejecuta las acciones correspondientes en esos actuadores.

PIOT-CDA-04-008: La clase DeviceDataManager gestiona y coordina todos los datos de los dispositivos, actuadores, sensores y rendimiento del sistema. Se encarga de recibir datos de los sensores, procesar comandos de los actuadores, y manejar los datos de rendimiento del sistema.

PIOT-CDA-04-009: Creeamos una instancia de DeviceDataManager dentro de ConstrainedDeviceApp u se invoca a los métodos de inicio/detención del administrador dentro de los métodos de inicio/detención de la aplicación.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule03

### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- ActuatorDataTest
- SensorDataTest
- SystemPerformanceDataTesIntegration Tests
- HumiditySensorSimTaskTest
- PressureSensorSimTaskTest
- TemperatureSensorSimTaskTest
- HumidifierActuatorSimTaskTest
- HvacActuatorSimTaskTest


### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- SensorAdapterManagerTest
- ActuatorAdapterManagerTest
- DeviceDataManagerNoCommsTest
- ConstrainedDeviceAppTest


EOF.
