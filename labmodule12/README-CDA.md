# Constrained Device Application (Connected Devices)

## Lab Module 12 - Semester Project - CDA Components

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

He implementado un nuevo sensor de luz ambiental y un actuador que responde a las medidas del sensor.

How does your implementation work?

Clase LightSensorSimTask
  - Usa valores generados entre LOW_NORMAL_ENV_LIGHT y HI_NORMAL_ENV_LIGHT.
  - Representa el sensor de luz cuando se usa en modo simulado.
  - Utiliza un dataSet opcional si se ha generado un conjunto de datos diario.

Clase LightSensorEmulatorTask
  - Usa la librería pisense para leer valores de luz del SenseHAT real o emulado.
  - Se usa cuando enableEmulator = True en el archivo de configuración.

Modificación en SensorDataGenerator
  - Añadi constantes como LOW_NORMAL_ENV_LIGHT y HI_NORMAL_ENV_LIGHT.
  - Implementé el método generateDailyEnvironmentLightDataSet para simular la curva de luz a lo largo del día.
  - Se usa en LightSensorSimTask.

Modificación en SensorAdapterManager
  - Añadi un atributo: self.lightAdapter.
  - Si el CDA está en modo simulador, cargo LightSensorSimTask con su dataset.
  - Si está en modo emulador, cargo LightSensorEmulatorTask.
  - En el método handleTelemetry(), se llama generateTelemetry() del sensor de luz y se reenvía el SensorData al IDataMessageListener.

LightActuatorSimTask.py
  - Hereda de BaseActuatorSimTask.
  - Simula el encendido/apagado de una fuente de luz.

LightEmulatorTask.py
  - Emula el efecto visual usando la pantalla LED del SenseHAT:
  - Enciende con mensaje: "LIGHT ON"
  - Apaga con mensaje: "LIGHT OFF"
  - Usa _activateActuator y _deactivateActuator.
  
ActuatorAdapterManager.py
  -Inicializa el actuador.
  - Recibe comandos y los redirige al actuador.

### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/antonoterof/python-components/tree/labmodule12


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

He añadido y modificado los siguientes test para comprobar el correcto funcionamiento:
- LightSensorEmulatorTaskTest
- LightActuatorEmulatorTaskTest
- ActuatorEmulatorManagerTest
- ActuatorAdapterManagerTest

EOF.
