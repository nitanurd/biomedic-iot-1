# Brokers
There are several brokers that we are going to try:  
- test.mosquito.org  
- local broker  
- serverles brokers (HiveMQ, EMQX, etc)  
- managed brokers (Blynk, Antares, etc)   
---
## Playing with test.mosquitto.org
Eclipse Mosquitto provides a simple broker in the [cloud](https://test.mosquitto.org/) that we can use for testings. In fact, test.mosquitto.org can be considered as another serverless broker. We will use port number 1883 for <u>unencrypted-unauthenticated</u> connections.

---
## Local broker
It is possible to install a broker in our own computer. In this case, our IoT devices connect to our computer directly. Internet connection becomes unnecessary.
### Setup the local broker
These are some good documentations on how to install the broker on Linux and Windows systems. <u>We must make sure that we allow anonymous connectios to the broker</u>.
#### Linux system:

[https://docs.vultr.com/install-mosquitto-mqtt-broker-on-ubuntu-20-04-server](https://docs.vultr.com/install-mosquitto-mqtt-broker-on-ubuntu-20-04-server)  
#### Windows system:

[https://cedalo.com/blog/how-to-install-mosquitto-mqtt-broker-on-windows/](https://cedalo.com/blog/how-to-install-mosquitto-mqtt-broker-on-windows/) -- follow step 1, 2, 5, 7, 8, 9, and 10. For step 5, follow only up to the point where we allow anonymous connections.

### Clients

One way to create a client is by simulating the client from the same computer. Here, we can use the `mosquitto_pub` and `mosquitto_sub` commands by using the following formats.  

- `mosquitto_pub -m PAYLOAD -t TOPIC`  
- `mosquitto_sub -t TOPIC`

To connect from our IoT devices, we can adapt the provided Arduino MQTT client template to connect to our local broker. <u>Please note that our local broker accepts anonymous connections</u>.
### Assignment
Adapt the provided Arduino MQTT client template to connect to your local broker.

---
## HiveMQ
### Create and run the broker
First, create a HiveMQ account for free and sign in to that account. After that, click  `Create New Cluster >> Manage Cluster`.  

![|650](attachments/Pasted%20image%2020241003075831.png)

In HiveMQ, clients must have accounts and passwords. Therefore, we need to create at least one  username and password which can be done from the `Access Management` menu.
![|600](attachments/Pasted%20image%2020241003080311.png)

---
## EMQX (assignment)

EMQX also provide a free serverless broker similar to HiveMQ.   
Now, let us setup a connection to EMQX broker from an Arduino MQTT client. <font color="#ff0000">This will become our class assignment</font>.  

----
## Blynk

