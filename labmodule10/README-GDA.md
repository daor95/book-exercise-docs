# Gateway Device Application (Connected Devices)

## Lab Module 10

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

**What does your implementation do?** This implementation focuses on securing MQTT communication and enhancing the data-handling logic within the GDA as part of Lab Module 10. A local Mosquitto MQTT broker was configured with TLS encryption (PIOT-CFG-10-001), including the generation of a certificate chain using OpenSSL and configuring Mosquitto to listen on port 8883. TLS functionality was validated using terminal-based MQTT clients (`mosquitto_pub` and `mosquitto_sub`) and further verified via Wireshark to confirm encrypted TLS handshake traffic. 

**How does your implementation work?** The `MqttClientConnector` was updated to support both authorization credentials and secure TLS connections. The client logic was refactored to use `MqttAsyncClient` to prevent deadlocks during asynchronous communication and to handle subscriptions to the CDA’s topics (`SensorData`, `SystemPerformanceData`, and `ActuatorData`) using a unified `messageArrived()` callback (Option 1). The `DeviceDataManager` was also partially updated to route incoming messages and begin handling them appropriately. While the detailed logic for humidity threshold analysis and actuation is not fully implemented yet, a test (`DeviceDataManagerSimpleCdaActuationTest`) was created to simulate sensor input. All updates were tested locally, reviewed, and merged successfully into the main development branch.


Steps:

- PIOT-CFG-10-001: A local MQTT broker using Mosquitto version 2.x with TLS encryption support has successfully been configured. The setup involved generating a test certificate chain (CA certificate, server certificate, and private key) using OpenSSL, and organizing these within a secure directory structure. Mosquitto config files have been eddited to reflect the new configuration activating TLS on port 8883.
The `mosquitto.conf` file was customized to include TLS settings via an `include_dir`, pointing to a `tls.conf` file containing the necessary certificate paths and listener port configuration (port 8883). The Mosquitto clients (`mosquitto_pub` and `mosquitto_sub`) were installed and used to verify secure message transmission. Successful TLS-based communication was confirmed through terminal-based publish/subscribe tests and log analysis, which indicated encrypted message exchange on the designated port. Wireshark was used to confirm TLS handshake activity.

![encrypted terminal](https://github.com/user-attachments/assets/af6b7c45-75e8-45e5-8876-858be9453010)

![encrypted wireshark](https://github.com/user-attachments/assets/6fae26ca-a183-4185-a19e-3af01dc53676)

```text
1748542023: New connection from 127.0.0.1:53438 on port 8883.
1748542023: New client connected from 127.0.0.1:53438 as auto-E4A07DD4-A8EA-92E8-CB84-CE8340E9B46A (p2, c1, k60).
1748542023: No will message specified.
1748542023: Sending CONNACK to auto-E4A07DD4-A8EA-92E8-CB84-CE8340E9B46A (0, 0)
1748542023: Received SUBSCRIBE from auto-E4A07DD4-A8EA-92E8-CB84-CE8340E9B46A
1748542023: 	test (QoS 0)
1748542023: auto-E4A07DD4-A8EA-92E8-CB84-CE8340E9B46A 0 test
1748542023: Sending SUBACK to auto-E4A07DD4-A8EA-92E8-CB84-CE8340E9B46A
1748542025: New connection from 127.0.0.1:53442 on port 8883.
1748542025: New client connected from 127.0.0.1:53442 as auto-5A91FB2A-8B0F-F22E-18FE-B4A123797BA7 (p2, c1, k60).
1748542025: No will message specified.
1748542025: Sending CONNACK to auto-5A91FB2A-8B0F-F22E-18FE-B4A123797BA7 (0, 0)
1748542025: Received PUBLISH from auto-5A91FB2A-8B0F-F22E-18FE-B4A123797BA7 (d0, q0, r0, m0, 'test', ... (4 bytes))
1748542025: Sending PUBLISH to auto-E4A07DD4-A8EA-92E8-CB84-CE8340E9B46A (d0, q0, r0, m0, 'test', ... (4 bytes))
1748542025: Received DISCONNECT from auto-5A91FB2A-8B0F-F22E-18FE-B4A123797BA7
1748542025: Client auto-5A91FB2A-8B0F-F22E-18FE-B4A123797BA7 disconnected.
```

- PIOT-GDA-10-000: Git "labmodule10" branch created.

- PIOT-GDA-10-001: The **MqttClientConnector** class was updated to support both authorization credentials and TLS-encrypted connections. This included integrating secure client parameter handling, loading credentials from external configuration files, and enabling certificate-based TLS using the `SimpleCertManagementUtil` utility. Several new configuration parameters and initialization methods (`initClientParameters`, `initSecureConnectionParameters`, and `initCredentialConnectionParameters`) were added to modularize and streamline setup.
Additionally, the no-argument constructor was modified to call `initClientParameters`, ensuring all connection parameters are loaded at instantiation. Current functionality was verified by re-running existing test `MqttClientConnectorTest` without TLS enabled.

- PIOT-GDA-10-002: The **MqttClientConnector** class was updated to subscribe to the CDA's MQTT topics for `SensorData`, `SystemPerformanceData`, and `ActuatorData` response messages. Option 1 was chosen for implementation, which handles all topic subscriptions through a single `messageArrived()` callback. Subscriptions were configured within the `connectComplete()` callback to ensure they are activated after a successful connection to the MQTT broker.
Additionally, the class was refactored to use `MqttAsyncClient` instead of the synchronous `MqttClient`, supporting non-blocking behavior and avoiding potential deadlocks during message publishing. The **DeviceDataManager** class was updated by commenting out redundant subscription logic. Functionality was verified by running the existing test `MqttClientConnectorTest` and adding a new test case to confirm that actuator response messages are correctly received and handled.

- PIOT-GDA-10-003: In this task, I began integrating logic into the `DeviceDataManager` to process incoming MQTT messages from the CDA, specifically `SensorData`, `SystemPerformanceData`, and `ActuatorData` responses. I opted for **Option 1**, which centralizes message handling in a single callback method. However, some of the detailed actions described in the task (including threshold crossing analysis, persistence logging, and upstream message forwarding) have not yet been implemented.
The current progress establishes the foundational structure for message routing through `handleIncomingMessage`, but the core logic such as humidity threshold checking, actuator command generation, and interaction with the persistence client remains to be completed. The next steps will involve implementing `handleIncomingDataAnalysis`, parsing the configuration file for humidity control parameters, and completing the logic needed to trigger actuation events based on time-series humidity data analysis.
To begin validating this behavior, a custom test method was created within a new class (`DeviceDataManagerSimpleCdaActuationTest`). This test simulates a sequence of `SensorData` messages with varying humidity values, including nominal and exceptional cases, to observe whether the actuation logic would be triggered once completed. However, since the threshold analysis and actuation logic are still pending, this test currently serves as a placeholder for future verification.

- PIOT-GDA-10-100: The code updates within the labmodule10 branch were reviewed and verified. All part01, part02 and part3 applicable unit and integration tests were executed successfully, ensuring the correctness of the implementation. A git merge was performed between labmodule10 and the primary branch, followed by pushing the merged changes to the remote repository. Finally, the merge was verified using "git log --oneline default", confirming that the merge history was correctly logged.




### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule10



### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 and part02 unit tests
- 
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 and part02 integration tests
- MqttClientConnectorTest
- DeviceDataManagerSimpleCdaActuationTest

EOF.
