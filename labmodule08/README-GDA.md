# Gateway Device Application (Connected Devices)

## Lab Module 08

Be sure to implement all the PIOT-GDA-* issues (requirements) listed.

### Description

NOTE: Include two full paragraphs describing your implementation approach by answering the questions listed below.

What does your implementation do? 

How does your implementation work?

Steps:

- PIOT-CFG-08-001: The Californium CoAP tools were successfully installed and configured on Ubuntu to facilitate initial testing of a CoAP server. The process involved cloning the Californium Tools repository from GitHub and building the project using Maven. Upon successful compilation, the key components (`cf-client` and `cf-server`) were verified to be generated within their respective `target` directories.

![coap_help](https://github.com/user-attachments/assets/6df15647-8e13-4235-94dc-479e22f384e4)

The command-line client tool was validated for functionality by executing it with the `--help` flag, confirming the availability of supported parameters. A functional test was performed by starting the CoAP server (`cf-server`) and sending a test GET request using the client (`cf-client`). Console outputs confirmed that the request was successfully sent and received, demonstrating correct setup and basic operation of the CoAP tools. The server was then properly terminated using standard interrupt commands.

Server:

![coap_server](https://github.com/user-attachments/assets/b7f9d357-6364-4dc2-9829-715fc6ecd91c)

Client:

![coap_client](https://github.com/user-attachments/assets/8f223da1-f296-46e3-9c31-efe3f1c09bb8)

```text
==[ CoAP Request ]=============================================
MID    : 28601
Token  : 345EE8D5F0473E6D
Type   : CON
Method : 0.01 - GET
Options: {"Uri-Host":"localhost"}
Payload: 0 Bytes
===============================================================

>>> UDP(localhost/127.0.0.1:5683)

Time elapsed (ms): 31
==[ CoAP Response ]============================================
MID    : 28601
Token  : 345EE8D5F0473E6D
Type   : ACK
Status : 2.05 - CONTENT
Options: {"Content-Format":"text/plain"}
RTT    : 31 ms
Payload: 471 Bytes
---------------------------------------------------------------
****************************************************************
CoAP RFC 7252                                  Cf 4.0.0-SNAPSHOT
****************************************************************
```


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
