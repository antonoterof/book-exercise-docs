# Constrained Device Application (Connected Devices)

## Lab Module 04

Be sure to implement all the PIOT-CDA-* issues (requirements).

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

Se configura un emulador del Sense HAT, que es un dispositivo que puede medir temperatura, humedad, presión, y más. El emulador simula estos datos, y la interfaz del emulador permite ajustar estos valores con deslizadores.
A su vez, se configuran tareas que simulan sensores y actuadores, para que se pueda probar el sistema sin necesidad de hardware físico. Los sensores emulados se encargan de generar datos de temperatura, humedad y presión, y los actuadores emulados simulan la activación y desactivación de dispositivos como HVAC y humidificadores. Cuando un actuador se activa o desactiva, un mensaje se muestra en el LED del emulador, lo que permite visualizar las acciones que está tomando el sistema.

How does your implementation work?

PIOT-CFG-04-001: Primero configuramos el emulador Sense-Emu para simular un Sense HAT, que es una placa de expansión para Raspberry Pi. Sense HAT es un hardware físico que se conecta a la Raspberry Pi para proporcionar lecturas de estos sensores. El emulador tiene una interfaz gráfica (GUI) que permite simular lecturas de sensores, lo cual es útil para probar sin necesidad de hardware real.

PIOT-CDA-04-001: En la primera tarea creamos tres clases (HumiditySensorEmulatorTask, PressureSensorEmulatorTask y TemperatureSensorEmulatorTask) que heredan de BaseSensorSimTask. Estas clases se encargan de simular datos de sensores. Se sobrecarga el método generateTelemetry() para recoger los datos de los sensores y devolverlos como un objeto SensorData.

PIOT-CDA-04-002: Luego, se crean y configuran actuadores emulados (HumidifierEmulatorTask, HvacEmulatorTas y LedDisplayEmulatorTask) que heredan de la clase base BaseActuatorSimTask. Controlan la activación y desactivación de los actuadores mostrando el estado en la pantalla LED del Sense-Emu.
PIOT-CDA-04-003: Se añade a la clase SensorAdapterManager la posibilidad de poder usar emuladores para generar los datos o simularlos. Para ello se debe establecer en la configuración de la aplicación la variable enableEmulator como verdadera.

PIOT-CDA-04-004: De la misma forma que en la tarea anterior, añadimos a la clase ActuatorAdapterManager soporte para actuadores emulados. Esto permite que, si se activa la opción de emulador en la configuración, el sistema cargue dinámicamente los actuadores emulados. Si no se habilita el emulador, se utilizan actuadores simulados.


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule04


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- 
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- SenseHatEmulatorQuickTest
- HumidityEmulatorTaskTest
- PressureEmulatorTaskTest
- TemperatureEmulatorTaskTest
- HumidifierEmulatorTaskTest
- HvacEmulatorTaskTest
- LedDisplayEmulatorTaskTest
- SensorEmulatorManagerTest
- ActuatorEmulatorManagerTest

EOF.
