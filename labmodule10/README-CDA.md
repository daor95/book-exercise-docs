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




### Code Repository and Branch

NOTE: Be sure to include the branch.

URL: 


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

- 
- 
- 

EOF.
