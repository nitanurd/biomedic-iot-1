# Brokers
There are several brokers that we are going to try:  
- test.mosquito.org  
- local broker  
- serverles brokers (HiveMQ, EMQX)  
- managed brokers (Blynk, Antares)  
## Playing with test.mosquitto.org
Eclipse Mosquitto provides a simple broker in the [cloud](https://test.mosquitto.org/) that we can use for testings. In fact, test.mosquitto.org can be considered as a serverless broker. Please note that at the moment, we will only use<u> the unencrypted-unauthenticated type of connection</u>. Thus, we will use the provided port number 1883.

---
## Local broker
It is possible to install a broker in our own computer. In this case, our IoT devices connect to our computer directly. Internet connection becomes unnecessary.
### Setup the local broker
This is a good documentations on how to install the broker on:    
#### Linux system:

[https://docs.vultr.com/install-mosquitto-mqtt-broker-on-ubuntu-20-04-server](https://docs.vultr.com/install-mosquitto-mqtt-broker-on-ubuntu-20-04-server)  
#### Windows system:

[https://cedalo.com/blog/how-to-install-mosquitto-mqtt-broker-on-windows/](https://cedalo.com/blog/how-to-install-mosquitto-mqtt-broker-on-windows/) -- follow step 1, 2, 5, 7, 8, 9, and 10. For step 5, follow only up to the point where we allow anonymous connections.

### Setup the clients

---
## Serverless brokers
### HiveMQ
With HiveMQ, only secure connection is allowed (see [this link.](https://community.hivemq.com/t/use-1883-port-instead-of-8883/2010/4))
![|650](attachments/Pasted%20image%2020241003075831.png)

Next, create several user ids and passwords from the `Access Management1 menu.
![|600](attachments/Pasted%20image%2020241003080311.png)

```cpp
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <WiFiClientSecure.h>

/****** WiFi Connection Details *******/
// This is a personal wifi, not an enterprise wifi!
// You may need to chabge this part to connect to the campus wifi.
const char* ssid = "wifi_name";
const char* password = "wifi_password";

/******* MQTT Broker Connection Details *******/
const char* mqtt_server = "bcf21ddabed1412aaf638a09b4732f50.s1.eu.hivemq.cloud";
const char* mqtt_username = "Device01";
const char* mqtt_password = "Device01";
const int mqtt_port = 8883;

/**** Secure WiFi Connectivity Initialisation *****/
WiFiClientSecure espClient;

/**** MQTT Client Initialisation Using WiFi Connection *****/
PubSubClient client(espClient);

unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE (50)
char msg[MSG_BUFFER_SIZE];

/************* Connect to WiFi ***********/
void setup_wifi() {
  delay(10);
  Serial.print("\nConnecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  randomSeed(micros());
  Serial.println("\nWiFi connected\nIP address: ");
  Serial.println(WiFi.localIP());
}

/************* Connect to MQTT Broker ***********/
void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Attempt to connect
    if (client.connect("1", mqtt_username, mqtt_password)) {
      Serial.println("connected");

      client.subscribe("topic1/#");   // subscribe the topics here

    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");   // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

/***** Call back Method for Receiving MQTT messages ****/
void subscriberCallback(char* topic, byte* payload, unsigned int length) {
  String incommingMessage = "";
  for (int i = 0; i < length; i++) incommingMessage+=(char)payload[i];

  Serial.println("Message arrived ["+String(topic)+"]"+incommingMessage);
}

/**** Method for Publishing MQTT Messages **********/
void publishMessage(const char* topic, String payload , boolean retained){
  if (client.publish(topic, payload.c_str(), true))
      Serial.println("Message publised ["+String(topic)+"]: "+payload);
}


/**** Application Initialisation Function******/
void setup() {
  Serial.begin(115200);
  while (!Serial) delay(1);
  setup_wifi();

  espClient.setInsecure();

  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(subscriberCallback);
}


/******** Main Function *************/
void loop() {

  if (!client.connected()) reconnect(); // check if client is connected
  client.loop();

  publishMessage("topic1/subtopic1", "789", true);

  delay(5000);

}
```

HiveMQ provides web client at:
	https://www.hivemq.com/demos/websocket-client/

Arduino tutorial:
	https://www.hivemq.com/blog/mqtt-on-arduino-nodemcu-esp8266-hivemq-cloud/#heading-connecting-the-node-mcu-board-to-the-arduino-ide-via-usb

---
### EMQX

----
## Managed brokers
### Blynk
### Antares

