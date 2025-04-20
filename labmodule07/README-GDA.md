# Gateway Device Application (Connected Devices)

## Lab Module 07

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-GDA-07-000: Git "labmodule07" branch created.
  
- PIOT-GDA-07-001: The **MqttClientConnector** class has been implemented within the `programmingtheiot.cda.connection` package to provide MQTT communication capabilities for the application. This class implements both the `IPubSubClient` and `MqttCallbackExtended` interfaces and includes logic for initializing and configuring a MQTT client using values retrieved from a configuration file. The constructor sets up the broker address, port, keep-alive interval, and other connection options such as clean sessions and auto-reconnect. The core functionality for establishing and terminating connections with the MQTT broker has been implemented in the `connectClient()` and `disconnectClient()` methods, with appropriate logging and exception handling. Placeholder methods for publishing, subscribing, unsubscribing, and message handling have been added, with detailed implementation to follow in future exercises. Additionally, a method to set the `IDataMessageListener` has been included, and the necessary callback methods for MQTT events have been stubbed out. This setup ensures that the class is ready for integration testing and can successfully establish and close connections with an MQTT broker as verified by the `testConnectAndDisconnect()` test.

- PIOT-GDA-07-002: The **MqttClientConnector** class has been updated to implement the MQTT callback methods defined by the `MqttCallbackExtended` interface. These include `connectComplete()`, which now logs a confirmation message when the client connects or reconnects to the broker; `connectionLost()`, which logs a warning when the connection to the broker is lost; and `deliveryComplete()`, which logs the successful delivery of a published message. Additionally, the `messageArrived()` method has been implemented to log a message whenever a new MQTT message is received on a subscribed topic. These enhancements allow the connector to provide real-time feedback on MQTT communication events, improving visibility into connection status and message flow. With these additions, the `testConnectAndDisconnect()` integration test confirms that the client not only connects and disconnects successfully but also logs relevant MQTT events.

- PIOT-GDA-07-003: The **MqttClientConnector** class has been enhanced to support MQTT publish and subscribe capabilities. The `publishMessage()` method was implemented to validate the topic and QoS level, convert the message to a byte payload, and publish it to the specified topic, with error handling and logging. Similarly, the `subscribeToTopic()` and `unsubscribeFromTopic()` methods were implemented to manage topic subscriptions and ensure valid parameters, while also logging the outcomes. A class-level `DEFAULT_QOS` constant was introduced to handle invalid QoS values. Additionally, the `isConnected()` method was completed to accurately reflect the client’s current connection status. These updates enable the class to participate fully in MQTT communication, including receiving and handling callbacks for successful delivery (`deliveryComplete`) and incoming messages (`messageArrived`). With these changes, the integration test `testPublishAndSubscribe()` now executes successfully, demonstrating that the class can reliably connect to a broker, manage topic subscriptions, publish messages, and receive them through proper callback handling.

- PIOT-GDA-07-004: The **MqttClientConnector** has been integrated into the **DeviceDataManager** to enable end-to-end MQTT-based communication within the Gateway Device Application (GDA). A class-scoped boolean flag `enableMqttClient` now controls whether MQTT functionality should be used, allowing dynamic configuration through `ConfigUtil`. When enabled, the `MqttClientConnector` is instantiated and initialized during the `initManager()` method, with the `DeviceDataManager` also registered as the `IDataMessageListener`. The `startManager()` method connects to the MQTT broker and subscribes to essential topics using default QoS, while `stopManager()` ensures clean disconnection by unsubscribing from the topics and terminating the connection. To validate the implementation, a dedicated integration test class `MqttClientControlPacketTest` was created, successfully exercising all 14 MQTT 3.1.1 control packet types through a sequence of connection, publishing with QoS levels 0, 1, and 2, subscription management, and ping handling based on the configured keep-alive interval.

- PIOT-CFG-07-001: Already completed on LabModule6.

- PIOT-STU-07-002: Using WireShark we capture the data packets like we did in LabModule6 with the CDA. The following is a summary as much of the explanation is already detailed on LabModule6.

![wiresharkJava1](https://github.com/user-attachments/assets/7402b0cb-e3e3-4a89-a425-1b9d15ea08f3)
![wiresharkJava2](https://github.com/user-attachments/assets/1dda8374-529d-49cc-ae32-ca0df19c7684)

The data packets recevied can be classified in one of the 14 Control Packets for MQTT:

| Control Packet | Wireshark Label       | Packet Numbers                         |
| -------------- | --------------------- | -------------------------------------- |
| CONNECT        | Connect Command       | 17, 33, 79                             |
| CONNACK        | Connect Ack           | 19, 35, 81                             |
| PUBLISH        | Publish Message       | 40, 41 (QoS 0); 48, 49 (QoS 1); 61, 65 (QoS 2) |
| PUBACK         | Publish Ack           | 51, 55                                 |
| PUBREC         | Publish Received      | 62, 66                                 |
| PUBREL         | Publish Release       | 64, 69                                 |
| PUBCOMP        | Publish Complete      | 67, 70                                 |
| SUBSCRIBE      | Subscribe Request     | 37, 45, 57                             |
| SUBACK         | Subscribe Ack         | 38, 46, 59                             |
| UNSUBSCRIBE    | Unsubscribe Request   | 43, 53, 68                             |
| UNSUBACK       | Unsubscribe Ack       | 44, 54, 71                             |
| PINGREQ        | Ping Request          | 23, 83                                 |
| PINGRESP       | Ping Response         | 24, 84                                 |
| DISCONNECT     | Disconnect Req        | 26, 72, 86                             |
  
If we divide it in cycles, we can see the differences among the different levels of QoS:

**First cycle (all control packets, QoS 0 where applicable):**

| Control Packet | Wireshark “Type”               | Packet No. (first cycle)      |
| -------------- | ------------------------------ | ----------------------------- |
| CONNECT        | Connect Command                | 17                            |
| CONNACK        | Connect Ack                    | 19                            |
| PUBLISH (QoS 0)| Publish Message (QoS=0)        | 40, 41                        |
| PUBACK         | —                              | *(none for QoS 0)*            |
| PUBREC         | —                              | *(none for QoS 0)*            |
| PUBREL         | —                              | *(none for QoS 0)*            |
| PUBCOMP        | —                              | *(none for QoS 0)*            |
| SUBSCRIBE      | Subscribe Request (id=1)       | 37                            |
| SUBACK         | Subscribe Ack (id=1)           | 38                            |
| UNSUBSCRIBE    | Unsubscribe Request (id=2)     | 43                            |
| UNSUBACK       | Unsubscribe Ack (id=2)         | 44                            |
| PINGREQ        | Ping Request                   | 23                            |
| PINGRESP       | Ping Response                  | 24                            |
| DISCONNECT     | Disconnect Req                 | 26                            |

---

**QoS 1 cycle**

| Control Packet    | Wireshark “Type”               | Packet No.  |
| ----------------- | ------------------------------ | ----------- |
| CONNECT           | Connect Command                | 33, 35      |
| SUBSCRIBE         | Subscribe Request (id=3)       | 45          |
| SUBACK            | Subscribe Ack (id=3)           | 46          |
| PUBLISH (QoS 1)   | Publish Message (QoS = 1)      | 48, 49      |
| PUBACK            | Publish Ack                    | 51, 55      |
| UNSUBSCRIBE       | Unsubscribe Request (id=5)     | 53          |
| UNSUBACK          | Unsubscribe Ack (id=5)         | 54          |
| DISCONNECT        | Disconnect Req                 | 72          |

---

**QoS 2 cycle**

| Control Packet      | Wireshark “Type”               | Packet No.  |
| ------------------- | ------------------------------ | ----------- |
| SUBSCRIBE           | Subscribe Request (id=6)       | 57          |
| SUBACK              | Subscribe Ack (id=6)           | 59          |
| PUBLISH (QoS 2)     | Publish Message (QoS = 2)      | 61, 65      |
| PUBREC              | Publish Received               | 62, 66      |
| PUBREL              | Publish Release                | 64, 69      |
| PUBCOMP             | Publish Complete               | 67, 70      |
| UNSUBSCRIBE         | Unsubscribe Request (id=8)     | 68          |
| UNSUBACK            | Unsubscribe Ack (id=8)         | 71          |
| DISCONNECT          | Disconnect Req                 | 86          |

---

Over the three test runs (first with QoS 0, then QoS 1, and finally QoS 2) all fourteen MQTT 3.1.1 control packet types and are captured in Wireshark.

1. **Initial cycle (QoS 0 where applicable)**  
   - **CONNECT/CONNACK**: The client establishes a clean session.  
   - **PUBLISH**: A simple, “fire‑and‑forget” PUBLISH at QoS 0 (no acknowledgment).  
   - **SUBSCRIBE/SUBACK** and **UNSUBSCRIBE/UNSUBACK**: Subscribe to, then unsubscribe from, the management topic.  
   - **PINGREQ/PINGRESP**: Letting the keep‑alive interval elapse forces a ping exchange to keep the TCP connection alive.  
   - **DISCONNECT**: The client cleanly ends the session.
  ```text
    MQ Telemetry Transport Protocol, Publish Message
    Header Flags: 0x30, Message Type: Publish Message, QoS Level: At most once delivery (Fire and Forget)
        0011 .... = Message Type: Publish Message (3)
        .... 0... = DUP Flag: Not set
        .... .00. = QoS Level: At most once delivery (Fire and Forget) (0)
        .... ...0 = Retain: Not set
    Msg Len: 57
    Topic Length: 36
    Topic: PIOT/ConstrainedDevice/MgmtStatusMsg
    Message: 54657374206d6573736167653a20516f532030
  ```
  

2. **Second cycle (QoS 1)**  
   - Reconnect, subscribe again, then issue a **PUBLISH at QoS 1**, which triggers the two‑step PUBLISH/PUBACK handshake.  
   - Afterward, unsubscribe and disconnect.
  ```text
    MQ Telemetry Transport Protocol, Publish Message
    Header Flags: 0x32, Message Type: Publish Message, QoS Level: At least once delivery (Acknowledged deliver)
        0011 .... = Message Type: Publish Message (3)
        .... 0... = DUP Flag: Not set
        .... .01. = QoS Level: At least once delivery (Acknowledged deliver) (1)
        .... ...0 = Retain: Not set
    Msg Len: 59
    Topic Length: 36
    Topic: PIOT/ConstrainedDevice/MgmtStatusMsg
    Message Identifier: 4
    Message: 54657374206d6573736167653a20516f532031
  ```

3. **Third cycle (QoS 2)**  
   - Reconnect, subscribe, then send a **PUBLISH at QoS 2**, which triggers the full four‑step handshake:  
     1. **PUBLISH** → 2. **PUBREC** → 3. **PUBREL** → 4. **PUBCOMP**  
   - Finally, unsubscribe and disconnect.
  ```text
    MQ Telemetry Transport Protocol, Publish Message
    Header Flags: 0x34, Message Type: Publish Message, QoS Level: Exactly once delivery (Assured Delivery)
        0011 .... = Message Type: Publish Message (3)
        .... 0... = DUP Flag: Not set
        .... .10. = QoS Level: Exactly once delivery (Assured Delivery) (2)
        .... ...0 = Retain: Not set
    Msg Len: 59
    Topic Length: 36
    Topic: PIOT/ConstrainedDevice/MgmtStatusMsg
    Message Identifier: 7
    Message: 54657374206d6573736167653a20516f532032
  ```

- PIOT-GDA-07-100: The code updates within the labmodule07 branch were reviewed and verified. All part01, part02 and lab7 applicable unit and integration tests were executed successfully, ensuring the correctness of the implementation. A git merge was performed between labmodule07 and the primary branch, followed by pushing the merged changes to the remote repository. Finally, the merge was verified using "git log --oneline default", confirming that the merge history was correctly logged.



### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/java-components/tree/labmodule07


### Unit Tests Executed

NOTE: The instructor will execute your unit tests. You only need to list each test case below
(e.g. ConfigUtilTest, DataUtilTest, etc). Be sure to include all previous tests, too,
since you need to ensure you haven't introduced regressions.

- All part01 unit tests
- All part02 unit tests
- 

### Integration Tests Executed

NOTE: The instructor will execute most of your integration tests using their own environment, with
some exceptions (such as your cloud connectivity tests). In such cases, they'll review
your code to ensure it's correct. As for the tests you execute, you only need to list each
test case below (e.g. SensorSimAdapterManagerTest, DeviceDataManagerTest, etc.)

- All part01 integration tests
- All part02 integration tests
- MqttClientConnectorTest (testConnectAndDisconnect())
- MqttClientConnectorTest (testPublishAndSubscribe())
- MqttClientControlPacketTest (custom test)

EOF.
