# Constrained Device Application (Connected Devices)

## Lab Module 10

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

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

- PIOT-CDA-10-000: Git "labmodule10" branch created.

- PIOT-CDA-10-001: The **MqttClientConnector** class in the CDA application was updated to support TLS-encrypted connections to an MQTT broker. This enhancement involved modifying the constructor to read encryption settings and certificate paths from the configuration file (`PiotConfig.props`). Additionally, the `connectClient()` method was extended to conditionally enable TLS using Python’s `ssl` module when encryption is configured.
Specifically, the implementation checks if TLS is enabled, sets the secure port, and applies the appropriate PEM certificate using `tls_set()`. The client connection logic remains backward compatible, falling back to an unencrypted connection if TLS setup fails. These changes were verified by rerunning the `MqttClientConnectorTest` without TLS enabled, ensuring the connector continues to operate correctly in non-secure mode.

- PIOT-CDA-10-002: The CDA was updated to support incoming `ActuatorData` command messages from the Gateway Device. This involved extending the **IDataMessageListener** interface with a new method, `handleActuatorCommandMessage()`, and implementing this method within the **DeviceDataManager** class. The implementation forwards the actuator command to the actuator manager for processing, after basic validation.
To verify the changes, Option 1 was used: disabling MQTT and CoAP communication through the `PiotConfig.props` configuration file. A new integration test script, `DeviceDataManagerCallbackTest`, was created based on an existing test case. This test successfully executed a sample actuator command and confirmed the expected log output, indicating that the actuator logic was triggered and processed correctly.

- PIOT-CDA-10-003: The **MqttClientConnector** class was enhanced to support receiving `ActuatorData` command messages from the GDA. To enable this, a listener interface (`IDataMessageListener`) was integrated via a new `setDataMessageListener()` method, allowing incoming messages to be routed to the appropriate handler in the `DeviceDataManager`.
A callback method, `onActuatorCommandMessage()`, was implemented to process received actuator command messages. This method decodes the JSON payload into an `ActuatorData` object using the `DataUtil` utility and forwards it to the configured listener. The MQTT client was also updated to subscribe to the appropriate actuator command topic upon a successful connection, using topic-specific callbacks to ensure targeted handling.
Additionally, the previously blocking `msgInfo.wait_for_publish()` call in the `publishMessage()` method was commented out to avoid deadlock and allow asynchronous message processing. The new functionality was verified using a dedicated integration test (`testNewActuatorCmdPubSub`), confirming that messages were correctly subscribed to, received, and routed, with expected log output validating the flow.





### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule10


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
- DeviceDataManagerCallbackTest

EOF.
