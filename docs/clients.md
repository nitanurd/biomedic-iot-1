# Clients
## Template for an Arduino MQTT client

The following code listing is the general template for MQTT clients by using the <u>Arduino PubSubClient librar</u>y.   

<font color="#ff0000">Adapt the client code by changing the code sections that have 3 exclamation marks ( !!! ).</font>

```cpp
#include <ESP8266WiFi.h>

#define CAMPUS_WIFI
//#define ANONYMOUS // enable this line for using anonymous connection !!!

#ifdef CAMPUS_WIFI
  extern "C" {
  #include "user_interface.h"
  #include "wpa2_enterprise.h"
  #include "c_types.h"
  }
#endif

#include <PubSubClient.h>

#ifdef ANONYNOUS
#include <WiFiClient.h>
#else
#include <WiFiClientSecure.h>
#endif
/******* MQTT Broker Connection Details *******/
// Change this section according to the  broker !!!

#ifdef ANONYMOUS
const int mqtt_port = 1883;
#else
const int mqtt_port = 8883;
#endif

const char* mqtt_server = "pf11f770.ala.asia-southeast1.emqxsl.com";
const char* mqtt_clientid = "Device01";
const char* mqtt_username = "Device01";
const char* mqtt_password = "Device01";

const char* wifi_id = "wifi_id";
const char* wifi_username = "wifi_username";
const char* wifi_password = "wifi_password";
/***********************************************/


/**** Secure WiFi Connectivity Initialisation *****/
#ifdef ANONYMOUS
WiFiClient espClient;
#else
WiFiClientSecure espClient;
#endif

/**** MQTT Client Initialisation Using WiFi Connection *****/
PubSubClient client(espClient);


/************* Connect to campus WiFi ***********/
void connect_to_campus_wifi_esp866(const char* ssid, const char* userid, const char* pwd)
{
  Serial.print("\nConnecting to ");
  Serial.println(ssid);
  
  WiFi.mode(WIFI_STA);

  wifi_set_opmode(STATION_MODE);  

  wifi_station_set_wpa2_enterprise_auth(1);
  
  wifi_station_set_enterprise_identity((uint8*)userid, strlen(userid));
  wifi_station_set_enterprise_username((uint8*)userid, strlen(userid));
  wifi_station_set_enterprise_password((uint8*)pwd, strlen((char*)pwd));

  wifi_station_connect();
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print("-");
  }

  Serial.println();
  Serial.println("\nWiFi connected\nIP address: ");
  Serial.println(WiFi.localIP());
}


/************* Connect to home WiFi ***********/
void connect_to_home_wifi(const char* ssid, const char* pwd)
{
  Serial.print("\nConnecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  
  WiFi.begin(ssid, pwd);

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
    
#ifdef ANONYMOUS
    if (client.connect(mqtt_clientid))
#else
    if (client.connect(mqtt_clientid, mqtt_username, mqtt_password))
#endif
      Serial.println("connected");
    else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");   // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}


/***** Call back Method for Receiving MQTT messages and Switching LED ****/
void callback(char* topic, byte* payload, unsigned int length) {
  String incommingMessage = "";
  for (int i = 0; i < length; i++) incommingMessage+=(char)payload[i];

  Serial.println("Message arrived ["+String(topic)+"]: "+incommingMessage);

}


/**** Method for Publishing MQTT Messages **********/
void publishMessage(const char* topic, String payload , boolean retained){
  if (client.publish(topic, payload.c_str(), true))
      Serial.println("Message published ["+String(topic)+"]: "+payload);
}


/**** Application Initialisation Function******/
void setup() {
  Serial.begin(115200);
  while (!Serial) delay(1);

#ifdef CAMPUS_WIFI
  connect_to_campus_wifi_esp866(wifi_id, wifi_username, wifi_password);
#else
  connect_to_home_wifi(wifi_id, wifi_password)
#endif

#ifndef ANONYMOUS
  espClient.setInsecure();
#endif

  // Connect to the broker
  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(callback);
}


/******** Main Function *************/
void loop() {
  if (!client.connected()) {
    reconnect();
    client.subscribe("topic2/#"); // Subscribe the topics here !!!
  }
  
  client.loop();

  // Publish something !!!
  publishMessage("topic1/subtopic1", "1234567890", true);

  delay(5000);
}
```