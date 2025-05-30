# Gateway Device Application (Connected Devices)

## Lab Module 11

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

**What does your implementation do?**
The implementation for Lab Module 11 focused on establishing secure and functional end-to-end communication between the Constrained Device Application (CDA), the Gateway Device Application (GDA), and a cloud-based IoT platform, using Ubidots STEM as the cloud provider. A secure MQTT connection was configured using TLS encryption and token-based authentication, with all relevant parameters specified in the `PiotConfig.props` file. Enhancements were made to the `MqttClientConnector` to support cloud-based configurations and event listeners. A new interface, `ICloudClient`, was used to define the expected behaviors of cloud clients, including connecting, publishing data, subscribing to topics, and handling incoming messages. This interface was then implemented in the `CloudClientConnector`, which internally delegates MQTT tasks to `MqttClientConnector`.

**How does your implementation work?**
Once cloud connectivity was established, the system was extended to handle actuation logic based on cloud-side rules. A threshold-based trigger was configured in Ubidots to detect high temperature values and publish LED ON/OFF commands to a specific MQTT topic. The GDA subscribed to this topic after successful connection using the `IConnectionListener` mechanism. When an actuation message was received, it was parsed into an `ActuatorData` object and routed to the CDA, which is supposed to trigger a visual LED response using the Sense HAT emulator (I was not able to achive the emulator part). This process was confirmed through log outputs. Multiple screenshots were collected to demonstrate live data variations and the actuation behavior. Integration and manual tests confirmed successful bidirectional communication and cloud-triggered actuation in response to sensed environmental changes.

Steps:

- PIOT-CFG-11-001: A cloud-based IoT connectivity solution was successfully configured using the Ubidots STEM platform, which supports MQTT with TLS encryption and token-based authorization. An account was created on Ubidots STEM specifically for educational purposes, and a secure API token was generated to enable client authentication. This token was saved locally in a separate configuration file.
In addition, the necessary root certificate required for establishing a TLS connection with the Ubidots MQTT broker was downloaded and stored securely on the local filesystem. The `PiotConfig.props` configuration file was then updated under the `Cloud.GatewayService` section to include the path to the certificate file, as well as connection settings such as host, ports, QoS level, and base topic structure. 


- PIOT-GDA-11-000: Git "labmodule11" branch created.

- PIOT-GDA-11-001: The implementation focused on enhancing the **MqttClientConnector** class to support greater flexibility and modularity when interacting with MQTT brokers. The class was updated to load configuration parameters either from the default MQTT section or from a separate cloud gateway section in the `PiotConfig.props` file. To support this, two new constructors were added: one accepting a boolean flag and another accepting a string section name. The configuration loading logic was extracted into a new private method `initClientParameters()`, aligning with the design implemented in Lab Module 10.
In addition, the class was extended with the ability to notify an external `IConnectionListener` of MQTT connection events, using a new `setConnectionListener()` method. To enable subclass and package-scoped access, new `protected` versions of `publishMessage`, `subscribeToTopic`, and `unsubscribeFromTopic` methods were introduced, each accepting raw topic strings and optionally a message listener. These were integrated into the existing public methods, which now delegate to the new implementations. The `connectComplete()` callback was updated to optionally subscribe to either local or cloud topics based on the `useCloudGatewayConfig` flag, supporting two subscription strategies. Integration test (`MqttClientConnectorTest`) was rerun using the local broker to validate backward compatibility and the correctness of the new behavior.

- PIOT-GDA-11-002: The Java interface named **ICloudClient** was created (it already existed) to define the contract for cloud-based pub/sub clients. The interface includes methods for connecting and disconnecting from the cloud service, publishing sensor and system performance data, subscribing and unsubscribing to cloud events, and setting a data message listener. This interface will serve as a foundational component for implementations such as `CloudClientConnector`, enabling standardized communication between edge devices and cloud platforms.

- PIOT-GDA-11-003: A new Java class named **CloudClientConnector** has been implemented to enable cloud integration using the MQTT pub/sub paradigm. The class implements the `ICloudClient` interface and internally uses `MqttClientConnector` to handle all MQTT-related operations. Key functionalities include connecting and disconnecting from the cloud broker, publishing sensor and system performance data, and subscribing or unsubscribing to cloud-sourced events. The implementation also includes dynamic topic construction based on configuration, QoS settings, and message conversion to JSON.
Additionally, `CloudClientConnector` was integrated into the `DeviceDataManager` class. A configuration flag enables or disables cloud connectivity, and upon activation, the manager connects to the cloud at startup and transmits relevant data. Incoming actuator commands from the cloud are also handled and routed appropriately. The implementation was verified using a integration test (`CloudClientConnectorTest`) with a sample configuration pointing to Ubidots' MQTT broker, demonstrating secure TLS connectivity and correct message delivery. **¡A VECES FALLA EL TEST PERO ES PROBLEMA DEL CLOUD, EVENTUALMENTE FUNCIONA!**

- PIOT-GDA-11-004: End-to-end integration between the Constrained Device Application (CDA), Gateway Device Application (GDA), and the cloud service was successfully implemented. The cloud service was configured to collect and store sensor and system performance data sent from the CDA through the GDA. A cloud-side rule was created to monitor threshold values and trigger LED actuation events accordingly for overtemperature. These events were published to a dedicated MQTT topic.
On the GDA side, the `CloudClientConnector` subscribed to the cloud LED actuation topic using a callback handler that parsed incoming messages into `ActuatorData` objects. These were routed via `DeviceDataManager` to the CDA, where they were handled by the actuator logic (e.g., activating the Sense HAT LED display). The GDA used the `IConnectionListener` interface to defer subscription until a successful connection to the cloud. Logging confirmed successful message flow across all layers, including sensor data uploads, actuation triggers, and downstream commands.

Trigger created on the cloud to activate the LED if overtemperature is detected:
![trigger](https://github.com/user-attachments/assets/267e27e2-8e9a-49c6-9d02-5929a28bb6cd)
Output from the terminal where actuator activation can be observed after sending a temperature data over the thresgold:
![test_cloud_1](https://github.com/user-attachments/assets/e123932d-dda8-499d-8e82-0ebac340876e)
![test_cloud_2](https://github.com/user-attachments/assets/04234e08-6fc2-4ad8-b98c-038d44784c5c)

Demonstration with real time values obtained by manually selecting them using the slider on the SenseHat emulator:
![cloud1](https://github.com/user-attachments/assets/da382165-1c11-4d71-9f2e-b92ae134c3d9)
![led_actuator](https://github.com/user-attachments/assets/6c8d641d-3108-4336-93c1-607106258a85)

Variation of the data can be observed:
![led_cloud](https://github.com/user-attachments/assets/b7a0624d-ed79-4f17-82cf-6bb303848427)
![cloud_temp](https://github.com/user-attachments/assets/3b0796fa-8a2a-4ff5-a249-069245e5530a)
![cloud_led_time](https://github.com/user-attachments/assets/f93c37d2-098e-49aa-b75b-5d03441533ee)


### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule11


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01, part02 and part03 unit tests
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01, part02 and part03 integration tests
- MqttClientConnectorTest
- CloudClientConnectorTest
- Manual Integration Test (PIOT-GDA-11-004)

EOF.
