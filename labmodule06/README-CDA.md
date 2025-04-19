# Constrained Device Application (Connected Devices)

## Lab Module 06

Be sure to implement all the PIOT-CDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?


Steps:

- PIOT-CDA-06-000: Git "labmodule05" branch created.
  
- PIOT-CDA-06-001: The **MqttClientConnector** class was implemented within the `programmingtheiot.cda.connection` package, following the `IPubSubClient` interface. The constructor was created to initialize the MQTT client properties by retrieving configuration values such as the host, port, keep-alive interval, and client ID from the `PiotConfig.props` file using the `ConfigUtil` class. The `connectClient()` method was implemented to establish a connection to the MQTT broker and start the MQTT network loop, while the `disconnectClient()` method was added to properly disconnect from the broker and stop the loop. The remaining interface methods—`publishMessage()`, `subscribeToTopic()`, and `setDataMessageListener()`—were stubbed with basic logging statements for future development. Finally, the `testConnectAndDisconnect()` integration test was run to verify that the MQTT client could successfully connect to and disconnect from the broker.

- PIOT-CDA-06-002: Callback methods were added to the **MqttClientConnector** class to handle key MQTT client events. These included `onConnect()`, `onDisconnect()`, `onMessage()`, `onPublish()`, and `onSubscribe()`, each responsible for logging relevant information during their respective events. The `onConnect()` method logs a message when a connection to the broker is established, while `onDisconnect()` confirms when the client disconnects. The `onMessage()` method logs incoming messages and decodes their payload, preparing for future data handling. The `onPublish()` and `onSubscribe()` methods provide confirmation logs when messages are published or when topic subscriptions are acknowledged. These callbacks were registered in the `connectClient()` method before establishing the MQTT connection. Once completed, the changes were verified using the `testConnectAndDisconnect()` integration test, confirming successful connection and disconnection along with appropriate event logging.

- PIOT-CDA-06-003: Full publish and subscribe capabilities were implemented in the **MqttClientConnector** class. The `publishMessage()` method was added to validate the topic and message content before publishing to the MQTT broker, ensuring the QoS level is within valid bounds or defaulting to a configured value if necessary. Similarly, the `subscribeToTopic()` method was implemented to allow topic subscriptions with appropriate validation and logging, while the `unsubscribeFromTopic()` method enables clean unsubscription from a topic. These methods ensure communication handling and integration with the MQTT broker. With these additions, the `testConnectAndCDAManagementStatusPubSub()` integration test was enabled and successfully executed, confirming that the publish, subscribe, and callback mechanisms—such as `onConnect()`, `onSubscribe()`, `onPublish()`, `onMessage()`, and `onDisconnect()`—are functioning as expected and producing the appropriate log output during test execution.

- PIOT-CDA-06-004: The **MqttClientConnector** was successfully integrated into the **DeviceDataManager** class to manage MQTT connectivity as part of the device lifecycle. Conditional logic was added in the constructor of `DeviceDataManager` to check the `ENABLE_MQTT_CLIENT_KEY` flag from the configuration file. If enabled, an instance of `MqttClientConnector` is created and linked with a `DataMessageListener`. In the `startManager()` method, the MQTT client is connected to the broker and subscribed to the `CDA_ACTUATOR_CMD_RESOURCE` topic, while in `stopManager()`, the client unsubscribes from the same topic and disconnects from the broker to ensure clean shutdown. To validate these changes and test full MQTT protocol compliance, a comprehensive test was implemented in the **MqttClientControlPacketTest** class. Includes tests for generating all MQTT 3.1.1 control packets—such as CONNECT, PUBLISH (QoS 1 and 2), SUBSCRIBE, PINGREQ, and DISCONNECT...

- PIOT-CFG-06-001: Test to check if the broker, publisher and subscriber are working properly. After installing mosquitto and starting the **mosquitto service** with `sudo systemctl start mosquitto`, on two different terminals acting as publisher and subscriber, I executed `mosquitto_sub -h localhost -t test/demo`and `mosquitto_pub -h localhost -t test/demo -m "Hello, MQTT!"`. As show in the screenshoots, the subscriber received the published message.

![mosquitto_pub](https://github.com/user-attachments/assets/62cdb551-b155-4d93-a7dd-acfcd74fcc13)
![mosquitto_sub](https://github.com/user-attachments/assets/5c35bb74-74f2-4118-890d-41bdd2cde557)

- PIOT-STU-06-002: Using WireShark we can obtain the following list of packets from the test:

![wireshark1](https://github.com/user-attachments/assets/d6cc26ea-6d0c-4e96-8936-cf0f4c4169e1)
![wireshark2](https://github.com/user-attachments/assets/1d6449c8-95a1-42ca-a749-6e23b481a319)
![wireshark3](https://github.com/user-attachments/assets/39ff4590-76a8-41ed-a402-038f1a5151d2)
![wireshark4](https://github.com/user-attachments/assets/585f9a36-9c7a-4247-9f11-cee503c57671)


| Nº  | Time (s)     | Source     | Destination  | Protocol | Length | Info                                                                 |
|-----|--------------|------------|--------------|----------|--------|----------------------------------------------------------------------|
| 4   | 0.000159401  | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 7   | 0.000261869  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 13  | 2.009060331  | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |
| 20  | 4.010799182  | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 25  | 4.010979838  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 29  | 4.011731046  | 127.0.0.1  | 127.0.0.1    | MQTT     | 107    | Subscribe Request (id=1) [PIOT/ConstrainedDevice/ActuatorCmd]        |
| 30  | 4.011795301  | 127.0.0.1  | 127.0.0.1    | MQTT     | 71     | Subscribe Ack (id=1)                                                 |
| 34  | 5.012399431  | 127.0.0.1  | 127.0.0.1    | MQTT     | 435    | Publish Message (id=2) [PIOT/ConstrainedDevice/ActuatorCmd]          |
| 35  | 5.012494526  | 127.0.0.1  | 127.0.0.1    | MQTT     | 435    | Publish Message (id=1) [PIOT/ConstrainedDevice/ActuatorCmd]          |
| 37  | 5.012533380  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Ack (id=2)                                                   |
| 41  | 5.013097953  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Ack (id=1)                                                   |
| 45  | 6.014408571  | 127.0.0.1  | 127.0.0.1    | MQTT     | 106    | Unsubscribe Request (id=3)                                           |
| 47  | 6.014502214  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Unsubscribe Ack (id=3)                                               |
| 51  | 7.015715638  | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |
| 61  | 8.020058161  | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 66  | 8.020164395  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 72  | 8.023499472  | 127.0.0.1  | 127.0.0.1    | MQTT     | 105    | Subscribe Request (id=4) [PIOT/ConstrainedDevice/SensorMsg]          |
| 73  | 8.023561413  | 127.0.0.1  | 127.0.0.1    | MQTT     | 71     | Subscribe Ack (id=4)                                                 |
| 77  | 9.024441054  | 127.0.0.1  | 127.0.0.1    | MQTT     | 369    | Publish Message (id=5) [PIOT/ConstrainedDevice/SensorMsg]            |
| 78  | 9.024529098  | 127.0.0.1  | 127.0.0.1    | MQTT     | 369    | Publish Message (id=1) [PIOT/ConstrainedDevice/SensorMsg]            | 
| 80  | 9.024557184  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Ack (id=5)                                                   |
| 84  | 9.024794612  | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Ack (id=1)                                                   |
| 88  | 10.025487803 | 127.0.0.1  | 127.0.0.1    | MQTT     | 104    | Unsubscribe Request (id=6)                                           |
| 90  | 10.025555894 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Unsubscribe Ack (id=6)                                               |
| 94  | 11.027545809 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |
| 103 | 12.029315850 | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 108 | 12.029436387 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 113 | 12.030061817 | 127.0.0.1  | 127.0.0.1    | MQTT     | 109    | Subscribe Request (id=7) [PIOT/ConstrainedDevice/SystemPerfMsg]      |
| 114 | 12.030137040 | 127.0.0.1  | 127.0.0.1    | MQTT     | 71     | Subscribe Ack (id=7)                                                 |
| 118 | 13.030857184 | 127.0.0.1  | 127.0.0.1    | MQTT     | 404    | Publish Message (id=8) [PIOT/ConstrainedDevice/SystemPerfMsg]        |
| 119 | 13.030999626 | 127.0.0.1  | 127.0.0.1    | MQTT     | 404    | Publish Message (id=1) [PIOT/ConstrainedDevice/SystemPerfMsg]        |
| 121 | 13.031039742 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Ack (id=8)                                                   |
| 125 | 13.031434335 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Ack (id=1)                                                   |
| 129 | 14.033227083 | 127.0.0.1  | 127.0.0.1    | MQTT     | 108    | Unsubscribe Request (id=9)                                           |
| 131 | 14.033348922 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Unsubscribe Ack (id=9)                                               |
| 135 | 15.036104769 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |
| 144 | 16.037092826 | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 149 | 16.037444829 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 154 | 16.038501431 | 127.0.0.1  | 127.0.0.1    | MQTT     | 109    | Subscribe Request (id=10) [PIOT/ConstrainedDevice/SystemPerfMsg]     |
| 155 | 16.038570794 | 127.0.0.1  | 127.0.0.1    | MQTT     | 71     | Subscribe Ack (id=10)                                                |
| 159 | 17.039217356 | 127.0.0.1  | 127.0.0.1    | MQTT     | 437    | Publish Message (id=11) [PIOT/ConstrainedDevice/SystemPerfMsg]       |
| 160 | 17.039333425 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Received (id=11)                                             |
| 164 | 17.039516143 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Release (id=11)                                              |
| 165 | 17.039587880 | 127.0.0.1  | 127.0.0.1    | MQTT     | 437    | Publish Message (id=1) [PIOT/ConstrainedDevice/SystemPerfMsg]        |
| 168 | 17.039762174 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Received (id=1)                                              |
| 169 | 17.039777108 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Complete (id=11)                                             |
| 171 | 17.079906304 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Release (id=1)                                               |
| 175 | 17.080299154 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Complete (id=1)                                              |
| 179 | 18.040417422 | 127.0.0.1  | 127.0.0.1    | MQTT     | 108    | Unsubscribe Request (id=12)                                          |
| 181 | 18.040529955 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Unsubscribe Ack (id=12)                                              |
| 185 | 19.041609613 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |
| 194 | 20.042947373 | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 199 | 20.043082824 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 204 | 20.043928460 | 127.0.0.1  | 127.0.0.1    | MQTT     | 105    | Subscribe Request (id=13) [PIOT/ConstrainedDevice/SensorMsg]         |
| 205 | 20.043992064 | 127.0.0.1  | 127.0.0.1    | MQTT     | 71     | Subscribe Ack (id=13)                                                |
| 209 | 21.048764325 | 127.0.0.1  | 127.0.0.1    | MQTT     | 369    | Publish Message (id=14) [PIOT/ConstrainedDevice/SensorMsg]           |
| 210 | 21.048966103 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Received (id=14)                                             |
| 214 | 21.049114474 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Release (id=14)                                              |
| 215 | 21.049187894 | 127.0.0.1  | 127.0.0.1    | MQTT     | 369    | Publish Message (id=1) [PIOT/ConstrainedDevice/SensorMsg]            |
| 218 | 21.049332750 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Received (id=1)                                              |
| 219 | 21.049345370 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Complete (id=14)                                             |
| 221 | 21.089953772 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Release (id=1)                                               |
| 225 | 21.090291662 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Complete (id=1)                                              |
| 229 | 22.050360503 | 127.0.0.1  | 127.0.0.1    | MQTT     | 104    | Unsubscribe Request (id=15)                                          |
| 231 | 22.050515886 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Unsubscribe Ack (id=15)                                              |
| 235 | 23.052237729 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |
| 244 | 24.054090306 | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 248 | 24.054184819 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 254 | 24.055017874 | 127.0.0.1  | 127.0.0.1    | MQTT     | 109    | Subscribe Request (id=16) [PIOT/ConstrainedDevice/SystemPerfMsg]     |
| 255 | 24.055073174 | 127.0.0.1  | 127.0.0.1    | MQTT     | 71     | Subscribe Ack (id=16)                                                |
| 259 | 25.055673568 | 127.0.0.1  | 127.0.0.1    | MQTT     | 404    | Publish Message (id=17) [PIOT/ConstrainedDevice/SystemPerfMsg]       |
| 260 | 25.055805222 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Received (id=17)                                             |
| 264 | 25.056035867 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Release (id=17)                                              |
| 265 | 25.056113363 | 127.0.0.1  | 127.0.0.1    | MQTT     | 404    | Publish Message (id=1) [PIOT/ConstrainedDevice/SystemPerfMsg]        |
| 268 | 25.056308611 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Received (id=1)                                              |
| 269 | 25.056381720 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Complete (id=17)                                             |
| 271 | 25.096938165 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Release (id=1)                                               |
| 275 | 25.097317292 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Publish Complete (id=1)                                              |
| 279 | 26.057084451 | 127.0.0.1  | 127.0.0.1    | MQTT     | 108    | Unsubscribe Request (id=18)                                          |
| 281 | 26.057177362 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Unsubscribe Ack (id=18)                                              |
| 285 | 27.058630771 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |
| 292 | 30.060821797 | 127.0.0.1  | 127.0.0.1    | MQTT     | 96     | Connect Command                                                      |
| 297 | 30.061039391 | 127.0.0.1  | 127.0.0.1    | MQTT     | 70     | Connect Ack                                                          |
| 301 | 90.177444530 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Ping Request                                                         |
| 302 | 90.177526893 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Ping Response                                                        |
| 306 | 95.184335663 | 127.0.0.1  | 127.0.0.1    | MQTT     | 68     | Disconnect Req                                                       |


With the following relationships of the 14 Control Packet Types (with a single example of each one):

| Control Packet Type | Wireshark Description    | Packet Number |
|---------------------|--------------------------|----------------|
| CONNECT             | Connect Command          | 4              |
| CONNACK             | Connect Ack              | 7              |
| PUBLISH             | Publish Message          | 34             |
| PUBACK              | Publish Ack              | 37             |
| PUBREC              | Publish Received         | 160            |
| PUBREL              | Publish Release          | 164            |
| PUBCOMP             | Publish Complete         | 169            |
| SUBSCRIBE           | Subscribe Request        | 29             |
| SUBACK              | Subscribe Ack            | 30             |
| UNSUBSCRIBE         | Unsubscribe Request      | 45             |
| UNSUBACK            | Unsubscribe Ack          | 47             |
| PINGREQ             | Ping Request             | 301            |
| PINGRESP            | Ping Response            | 302            |
| DISCONNECT          | Disconnect Req           | 13             |

**CONNECT**: 
```text
MQ Telemetry Transport Protocol, Connect Command
    Header Flags: 0x10, Message Type: Connect Command
        0001 .... = Message Type: Connect Command (1)
        .... 0000 = Reserved: 0
    Msg Len: 28
    Protocol Name Length: 4
    Protocol Name: MQTT
    Version: MQTT v3.1.1 (4)
    Connect Flags: 0x02, QoS Level: At most once delivery (Fire and Forget), Clean Session Flag
        0... .... = User Name Flag: Not set
        .0.. .... = Password Flag: Not set
        ..0. .... = Will Retain: Not set
        ...0 0... = QoS Level: At most once delivery (Fire and Forget) (0)
        .... .0.. = Will Flag: Not set
        .... ..1. = Clean Session Flag: Set
        .... ...0 = (Reserved): Not set
    Keep Alive: 60
    Client ID Length: 16
    Client ID: MyTestMqttClient
```

**CONNACK**:
```text
MQ Telemetry Transport Protocol, Connect Ack
    Header Flags: 0x20, Message Type: Connect Ack
        0010 .... = Message Type: Connect Ack (2)
        .... 0000 = Reserved: 0
    Msg Len: 2
    Acknowledge Flags: 0x00
        0000 000. = Reserved: Not set
        .... ...0 = Session Present: Not set
    Return Code: Connection Accepted (0)
```
**PUBLISH**: 
```text
MQ Telemetry Transport Protocol, Publish Message
    Header Flags: 0x32, Message Type: Publish Message, QoS Level: At least once delivery (Acknowledged deliver)
        0011 .... = Message Type: Publish Message (3)
        .... 0... = DUP Flag: Not set
        .... .01. = QoS Level: At least once delivery (Acknowledged deliver) (1)
        .... ...0 = Retain: Not set
    Msg Len: 366
    Topic Length: 34
    Topic: PIOT/ConstrainedDevice/ActuatorCmd
    Message Identifier: 2
    Message [truncated]: 7b0a202020202274696d655374616d70223a2022323032352d30342d31395431383a31313a33382e3139313334362b30303a3030222c0a20202020226861734572726f72223a2066616c73652c0a20202020226e616d65223a20224e6f7420536574222c0a2020202022747970
```
**PUBACK**:
```text
MQ Telemetry Transport Protocol, Publish Ack
    Header Flags: 0x40, Message Type: Publish Ack
        0100 .... = Message Type: Publish Ack (4)
        .... 0000 = Reserved: 0
    Msg Len: 2
    Message Identifier: 2
```
**PUBREC**:
```text
MQ Telemetry Transport Protocol, Publish Received
    Header Flags: 0x50, Message Type: Publish Received
        0101 .... = Message Type: Publish Received (5)
        .... 0000 = Reserved: 0
    Msg Len: 2
    Message Identifier: 11
```
**PUBREL**:
```text
MQ Telemetry Transport Protocol, Publish Release
    Header Flags: 0x62, Message Type: Publish Release
        0110 .... = Message Type: Publish Release (6)
        .... 0010 = Reserved: 2
    Msg Len: 2
    Message Identifier: 11
```
**PUBCOMP**:
```text
MQ Telemetry Transport Protocol, Publish Complete
    Header Flags: 0x70, Message Type: Publish Complete
        0111 .... = Message Type: Publish Complete (7)
        .... 0000 = Reserved: 0
    Msg Len: 2
    Message Identifier: 11
```
**SUBSCRIBE**:
```text
MQ Telemetry Transport Protocol, Subscribe Request
    Header Flags: 0x82, Message Type: Subscribe Request
        1000 .... = Message Type: Subscribe Request (8)
        .... 0010 = Reserved: 2
    Msg Len: 39
    Message Identifier: 1
    Topic Length: 34
    Topic: PIOT/ConstrainedDevice/ActuatorCmd
    Requested QoS: At least once delivery (Acknowledged deliver) (1)
```
**SUBACK**:
```text
MQ Telemetry Transport Protocol, Subscribe Ack
    Header Flags: 0x90, Message Type: Subscribe Ack
        1001 .... = Message Type: Subscribe Ack (9)
        .... 0000 = Reserved: 0
    Msg Len: 3
    Message Identifier: 1
    Granted QoS: At least once delivery (Acknowledged deliver) (1)
```
**UNSUBSCRIBE**:
```text
MQ Telemetry Transport Protocol, Unsubscribe Request
    Header Flags: 0xa2, Message Type: Unsubscribe Request
        1010 .... = Message Type: Unsubscribe Request (10)
        .... 0010 = Reserved: 2
    Msg Len: 38
    Message Identifier: 3
    Topic Length: 34
    Topic: PIOT/ConstrainedDevice/ActuatorCmd
```
**UNSUBACK**:
```text
MQ Telemetry Transport Protocol, Unsubscribe Ack
    Header Flags: 0xb0, Message Type: Unsubscribe Ack
        1011 .... = Message Type: Unsubscribe Ack (11)
        .... 0000 = Reserved: 0
    Msg Len: 2
    Message Identifier: 3
```
**PINGREQ**:
```text
MQ Telemetry Transport Protocol, Ping Request
    Header Flags: 0xc0, Message Type: Ping Request
        1100 .... = Message Type: Ping Request (12)
        .... 0000 = Reserved: 0
    Msg Len: 0
```
**PINGRESP**:
```text
MQ Telemetry Transport Protocol, Ping Response
    Header Flags: 0xd0, Message Type: Ping Response
        1101 .... = Message Type: Ping Response (13)
        .... 0000 = Reserved: 0
    Msg Len: 0
```
**DISCONNECT**:
```text
MQ Telemetry Transport Protocol, Disconnect Req
    Header Flags: 0xe0, Message Type: Disconnect Req
        1110 .... = Message Type: Disconnect Req (14)
        .... 0000 = Reserved: 0
    Msg Len: 0
```

| Type ID | Control Packet         | Description                                                                |
|---------|------------------------|----------------------------------------------------------------------------|
| 1       | CONNECT                | Client request to connect to the broker.                                   |
| 2       | CONNACK                | Broker acknowledgment of the connection request.                           |
| 3       | PUBLISH                | Publish a message to a topic.                                              |
| 4       | PUBACK                 | Acknowledgment for PUBLISH (QoS 1).                                        |
| 5       | PUBREC                 | Received confirmation for PUBLISH (QoS 2, step 1).                         |
| 6       | PUBREL                 | Release message for PUBLISH (QoS 2, step 2).                               |
| 7       | PUBCOMP                | Complete message for PUBLISH (QoS 2, step 3).                              |
| 8       | SUBSCRIBE              | Client request to subscribe to topics.                                     |
| 9       | SUBACK                 | Broker acknowledgment of subscription.                                     |
| 10      | UNSUBSCRIBE            | Client request to unsubscribe from topics.                                 |
| 11      | UNSUBACK               | Broker acknowledgment of unsubscription.                                   |
| 12      | PINGREQ                | Ping request to check connection/liveness.                                 |
| 13      | PINGRESP               | Ping response sent by the broker.                                          |
| 14      | DISCONNECT             | Client notification of disconnecting from broker.                          |

For QoS 0 (At most once, no ACK) only a PUBLISH packet is seen.

For QoS 1 (At least once, requires PUBACK) a PUBLISH followed by PUBACK is seen.

For QoS 2 (Exactly once, requires full handshake) PUBLISH → PUBREC → PUBREL → PUBCOMP is seen.


We can see the see **PUBLISH → PUBREC → PUBREL → PUBCOMP** in Wireshark because **QoS 2 requires a four-step handshake** to ensure:
1. Message is received exactly once.
2. No duplicate messages are delivered.

Also, in the **Keep-Alive Messages**:
1. By default, the MQTT client sends a **PINGREQ** to the broker periodically.
2. The broker responds with **PINGRESP**.

- PIOT-CDA-06-100: The code updates within the labmodule06 branch were reviewed and verified. All part01, part02 and lab6 applicable unit and integration tests were executed successfully, ensuring the correctness of the implementation. A git merge was performed between labmodule06 and the primary branch, followed by pushing the merged changes to the remote repository. Finally, the merge was verified using "git log --oneline default", confirming that the merge history was correctly logged.




### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: https://github.com/daor95/python-components/tree/labmodule06


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
- MqttClientConnectorTest (testConnectAndCDAManagementStatusPubSub())
- MqttClientControlPacketTest (custom test)

EOF.
