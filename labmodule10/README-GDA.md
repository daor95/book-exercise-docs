# Gateway Device Application (Connected Devices)

## Lab Module 10

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

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

- PIOT-GDA-10-000: Git "labmodule10" branch created.



### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: 



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
- 
- 

EOF.
