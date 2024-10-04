# First-time setup
Before we start playing with the MQTT protocol for our IoT class, there are some initial preparations that we must do.
## The supported devices

These are some microcontrollers with IoT capabilities: from left to right: ESP 32, Lolin Wemos D1 Mini Clone, and Arduino Uno R4 WiFi. 
<u>Do not forget to get the correct USB cable</u>.

|                                                                 |                                                                                    |                                                                              |
| --------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| ![ESP 32\|240](attachments/Pasted%20image%2020241002173452.png) | ![Lolin Wemos D1 Mini Clone\|150](attachments/Pasted%20image%2020241002173115.png) | ![Arduono Uno R4 WiFi\|210](attachments/Pasted%20image%2020241001140659.png) |

## Arduino IDE
Download and install the Arduino IDE from the [official website](https://www.arduino.cc/en/software) .
### Additional board manager

ESP 8266 and ESP 32 are not in the default Arduino board list.

For ESP 8266, add `http://arduino.esp8266.com/stable/package_esp8266com_index.json` to the `Additional Boards Manager URLs`, which can be found in this menu: `File >> "Preferences >> Settings`

As for ESP 32, add `https://espressif.github.io/arduino-esp32/package_esp32_index.json` to the `Additional Boards Manager URLs`

![|650](attachments/Pasted%20image%2020241001145040.png)
### ESP 32

Follow this official documentation form Espressif:  
[https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html)
### ESP 8266

Follow this official documentation form Espressif:  
[https://arduino-esp8266.readthedocs.io/en/latest/installing.html](https://arduino-esp8266.readthedocs.io/en/latest/installing.html)

The cheapest 8266 board is the clone version which uses CH340/341 chip for communication purposes.

In this case, if you are using Windows, follow this instruction to install the necessary driver:  
- Video guide: [https://www.youtube.com/watch?v=IxH_1LApjPc](https://www.youtube.com/watch?v=IxH_1LApjPc)  
- Driver:  [v3.4](attachments/CH341SER_WIN_3.4.zip) or [v3.5](attachments/CH341SER_WIN_3.5.zip)

There are some reports that the latest driver is problematic. Thus, we will use v3.4 or v3.5. 

Finally, in the Arduino IDE, we can now set the device to: "LOLIN (WEMOS) D1 mini (clone)". Do this from the menu `Tools >> Board >> ESP 8266 Boards (3.1.2) >> LOLIN (WEMOS) D1 mini (clone)`

### Arduino UNO R4 WiFi

The official  instruction can be found here:  
[https://docs.arduino.cc/tutorials/uno-r4-wifi/r4-wifi-getting-started/](https://docs.arduino.cc/tutorials/uno-r4-wifi/r4-wifi-getting-started/)
## Connecting to the University WiFi

Our university uses enterprise WiFi in its campus.  Depending on your device, use the following codes to connect to the university WiFi:
### ESP 8266
```cpp
#include  <ESP8266WiFi.h>

extern "C" {
#include "user_interface.h"
#include "wpa2_enterprise.h"
#include "c_types.h"
}

// SSID to connect to
char ssid[] = "TelU-Connect";
char username[] = "your_username";
char identity[] = "your_username";
char password[] = "your_password";


void setup() {

  WiFi.mode(WIFI_STA);
  Serial.begin(115200);
  delay(10);
  
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  // Setting ESP into STATION mode only (no AP mode or dual mode)
  wifi_set_opmode(STATION_MODE);  
  
  wifi_station_set_wpa2_enterprise_auth(1);
  
  wifi_station_set_enterprise_identity((uint8*)identity, strlen(identity));
  wifi_station_set_enterprise_username((uint8*)username, strlen(username));
  wifi_station_set_enterprise_password((uint8*)password, strlen((char*)password));

  wifi_station_connect();
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print("-");
  }

  Serial.println();
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void loop() {
}
```
### ESP 32

```cpp
#include "esp_eap_client.h"
#include <WiFi.h>

const char* ssid = "TelU-Connect"; // your ssid
#define EAP_ID "USERNAME"
#define EAP_USERNAME "USERNAME"
#define EAP_PASSWORD "PASSWORD"

void setup() {
    Serial.begin(115200);
    delay(10);

    Serial.println();
    Serial.print("Connecting to ");
    Serial.println(ssid);
    
    // WPA2 enterprise magic starts here
    WiFi.disconnect(true);      
    esp_eap_client_set_identity((uint8_t *)EAP_ID, strlen(EAP_ID));
    esp_eap_client_set_username((uint8_t *)EAP_USERNAME, strlen(EAP_USERNAME));
    esp_eap_client_set_password((uint8_t *)EAP_PASSWORD, strlen(EAP_PASSWORD));
    esp_wifi_sta_enterprise_enable();
    // WPA2 enterprise magic ends here

    WiFi.begin(ssid);

    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }

    Serial.println("");
    Serial.println("WiFi connected");
    Serial.println("IP address: ");
    Serial.println(WiFi.localIP());
}
```

### Uno R4 WiFi
## Assignment
Create a reusable function that can be used easily to connect to the campus internet. The prototype of the function should look like one of the followings:  
- `void connect_to_campus_wifi_esp866(char* ssid, char* id, char* pwd)`  
- `void connect_to_campus_wifi_esp32(char* ssid, char* id, char* pwd)`  
- `void connect_to_campus_wifi_unor4(char* ssid, char* id, char* pwd)`  