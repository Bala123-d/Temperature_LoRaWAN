# INTERFACING TEMPERATURE SENSOR WITH IOT CONTROLLER AND UPLOADING DATA TO THE CLOUD VIA LORAWAN
# NAME: D.BALA SUBRAMANYAM
# REG.NO: 212224040062
# AIM:
To upload the temperature sensor value in the Things mate using Arduino controller.

# Apparatus required:
Arduino Controller  </br>
Indoor gateway</br>
LoRaWAN shield </br>
DHT11 Temperature/Humidity sensor module </br>
Power supply </br>
Connecting wires </br>
Bread board </br>

# PROCEDURE:

### Procedure for gateway setup
1. Go to the link http://172.31.255.254:8000 </br>
2. Type the user name password </br>
3. Go to LoRa and set the frequency plan 865 Mhz </br>
4. In LoRa WAN configurations enter the Gate EUI and server address </br>
5. Enable the Internet </br>
6. Select the Wifi access point </br>
7. Type the ssid and password in wifi LAN setting </br>
8. Select the internet and IoT service and provide the details. </br>
9. Check all the green colour tick marks in gate way and proceed </br>
### Procedure for gateway registration in The thingsMate LoRaWAN Management </br>
1. Login https://iot.saveetha.in:4433 and provide the user id and password </br>
2. Go to overview and select application server </br>
3. Go to gateway and add new gateway </br>
4. Enter the gateway id, name, EUI and select frequency plan </br>
5. Open Arduino IDE and type the program for the given application </br>
6. Compile the program if no error uploads the program in the controller </br>
7. Go to gateways in things mate and check the live data </br>
8. Create channel by giving channel name and ID </br>
9. Add end device and enter the frequency plan, DevEUI, AppEUI, APP key, Select LoRaWAN version </br>
10. Enter payload formatters </br>
11. Go to the option query and give the new query name </br>
12. Go to the option Dashboard and verify the output.</br>  

# THEORY:

### What is IoT?

Internet of Things (IoT) describes an emerging trend where a large number of embedded devices (things) are connected to the Internet. These connected devices communicate with people and other things and often provide sensor data to cloud storage and cloud computing resources where the data is processed and analyzed to gain important insights. Cheap cloud computing power and increased device connectivity is enabling this trend.IoT solutions are built for many vertical applications such as environmental monitoring and control, health monitoring, vehicle fleet monitoring, industrial monitoring and control, and home automation

![IoT-Image](https://github.com/user-attachments/assets/444c0d14-7ff1-448d-9603-a9015dbb1cb4)


### What is LoRaWAN

The LoRaWAN® specification is a Low Power, Wide Area (LPWA) networking protocol designed to wirelessly connect battery operated ‘things’ to the internet in regional, national or global networks, and targets key Internet of Things (IoT) requirements such as bi-directional communication, end-to-end security, mobility and localization services.LoRaWAN® network architecture is deployed in a star-of-stars topology in which gateways relay messages between end-devices and a central network server. The gateways are connected to the network server via standard IP connections and act as a transparent bridge, simply converting RF packets to IP packets and vice versa. The wireless communication takes advantage of the Long Range characteristics of the LoRaÒ physical layer, allowing a single-hop link between the end-device and one or many gateways. All modes are capable of bi-directional communication, and there is support for multicast addressing groups to make efficient use of spectrum during tasks such as Firmware Over-The-Air (FOTA) upgrades or other mass distribution messages.

The specification defines the device-to-infrastructure (LoRa®) physical layer parameters & (LoRaWAN®) protocol and so provides seamless interoperability between manufacturers, as demonstrated via the device certification program.While the specification defines the technical implementation, it does not define any commercial model or type of deployment (public, shared, private, enterprise) and so offers the industry the freedom to innovate and differentiate how it is used.The LoRaWAN® specification is developed and maintained by the LoRa Alliance®: an open association of collaborating members.

![LoRaWAN_Image](https://github.com/user-attachments/assets/6ea13669-5b24-4916-ac73-29db9de17850)

### Characteristics of LoRaWAN technology
Long range communication up to 10 miles in line of sight.
Long battery duration of up to 10 years. For enhanced battery life, you can operate your devices in class A or class B mode, which requires increased downlink latency.
Low cost for devices and maintenance.
License-free radio spectrum but region-specific regulations apply.
Low power but has a limited payload size of 51 bytes to 241 bytes depending on the data rate. The data rate can be 0,3 Kbit/s – 27 Kbit/s data rate with a 222 maximal payload size.

### The Things Mate - IoT Cloud Platform

IoT cloud platforms play a pivotal role in the development and deployment of Internet of Things (IoT) applications, connecting devices and enabling seamless data management and analysis. These platforms typically offer a comprehensive suite of services, including device provisioning, secure connectivity, data storage, and advanced analytics.Leading IoT cloud platforms, such as AWS IoT, Azure IoT, and Google Cloud IoT, provide scalable and reliable infrastructure to accommodate diverse IoT deployments. They facilitate device management, allowing users to monitor, update, and control connected devices remotely. Security features are integral, ensuring data integrity and safeguarding against potential threats.

Analytics capabilities enable organizations to derive meaningful insights from the vast amounts of data generated by IoT devices. Machine learning and artificial intelligence integrations further enhance predictive analytics, enabling proactive decision-making.These platforms often offer APIs for seamless integration with other cloud services, supporting a wide range of industries and applications, from smart homes to industrial automation. As the IoT landscape evolves, cloud platforms continue to innovate, contributing to the growth and sophistication of IoT ecosystems worldwide. Choosing the right IoT cloud platform involves considering factors such as scalability, security, and compatibility with specific use cases.

### DHT11 Theory
The DHT11 is a basic, low-cost digital sensor used to measure temperature and humidity. It uses a capacitive humidity sensor and a thermistor to detect the surrounding air and sends the data through a single digital signal. The sensor has a built-in ADC (Analog to Digital Converter), which converts the analog signals to digital output, making it easy to interface with microcontrollers like Arduino.

Temperature range: 0–50°C (±2°C accuracy)</br>
Humidity range: 20–90% RH (±5% accuracy)</br>
Interface: Single-wire digital communication</br>
Update rate: 1 Hz (one reading per second)</br>

![DHT11-Sensor](https://github.com/user-attachments/assets/69e4670d-6116-4cab-b905-941169d913a5)

# PROGRAM:
```
#include <SoftwareSerial.h>
#include <Adafruit_Sensor.h>

#define triggerpin 8                 // trigger pin connected to the ultrosonic sensor 
#define echopin 9                   // techo pin connected to the ultrosonic sensor 

int duration, inches, cm;
String inputString = "";         // a String to hold incoming data
bool stringComplete = false;     // whether the string is complete
long old_time=millis();
long new_time;
long uplink_interval=30000;      //ms
bool time_to_at_recvb=false;
bool get_LA66_data_status=false;
bool network_joined_status=false;
char rxbuff[128];
uint8_t rxbuff_index=0;

SoftwareSerial ss(10, 11);       // Create a SoftwareSerial port on Arduino pins 10 (RX) and 11 (TX)

void setup() {
  pinMode(triggerpin,OUTPUT);
  pinMode(echopin,INPUT);
  Serial.begin(9600);
  ss.begin(9600);
  ss.listen();

  inputString.reserve(200);
  sensor_t sensor;
  ss.println("ATZ");//reset LA66
}

void loop() {
new_time = millis();
if((new_time-old_time>=uplink_interval)&&(network_joined_status==1)){
    old_time = new_time;
    get_LA66_data_status=false;
    HC04();      
    char sensor_data_buff[128]="\0";            
    snprintf(sensor_data_buff,128,"AT+SENDB=%d,%d,%d,%02X%02X",0,2,2,(short)(inches),(short)(cm));
    ss.println(sensor_data_buff);
  }
  if(time_to_at_recvb==true){
    time_to_at_recvb=false;
    get_LA66_data_status=true;
    delay(1000);    
    ss.println("AT+CFG");    
  }
    while ( ss.available()) {
    char inChar = (char) ss.read();
     inputString += inChar;
    rxbuff[rxbuff_index++]=inChar;
    if(rxbuff_index>128)
    break;
    
      if (inChar == '\n' || inChar == '\r') {
      stringComplete = true;
      rxbuff[rxbuff_index]='\0';
       if(strncmp(rxbuff,"JOINED",6)==0){
        network_joined_status=1;
      }
      if(strncmp(rxbuff,"Dragino LA66 Device",19)==0){
        network_joined_status=0;
      }
      if(strncmp(rxbuff,"Run AT+RECVB=? to see detail",28)==0){
        time_to_at_recvb=true;
        stringComplete=false;
        inputString = "\0";
      }
      if(strncmp(rxbuff,"AT+RECVB=",9)==0){       
        stringComplete=false;
        inputString = "\0";
        Serial.print("\r\nGet downlink data(FPort & Payload) ");
        Serial.println(&rxbuff[9]);
      }
       rxbuff_index=0;
      if(get_LA66_data_status==true){
        stringComplete=false;
        inputString = "\0";
      }
    }
  }

   while ( Serial.available()) {
    char inChar = (char) Serial.read();
    inputString += inChar;
    if (inChar == '\n' || inChar == '\r') {
      ss.print(inputString);
      inputString = "\0";
    }
  }
 
  if (stringComplete) {
    Serial.print(inputString);
    
    // clear the string:
    inputString = "\0";
    stringComplete = false;
  }
}

void HC04()
{
   digitalWrite(triggerpin, LOW);
   delayMicroseconds(2);
   digitalWrite(triggerpin, HIGH);
   delayMicroseconds(10);
   digitalWrite(triggerpin, LOW);
   duration = pulseIn(echopin, HIGH);
   inches = microsecondsToInches(duration);
   cm = microsecondsToCentimeters(duration);
   Serial.print(inches);
   Serial.print("in, ");
   Serial.print(cm);
   Serial.print("cm");
   Serial.println();
}
long microsecondsToInches(long microseconds) 
{
   return microseconds / 74 / 2;
}
long microsecondsToCentimeters(long microseconds) 
{
   return microseconds / 29 / 2;
}

/*function Decoder(bytes, port) {
  // Extract distance from the first two bytes
  var distance = (bytes[0] << 8) + bytes[1];

  // Convert to centimeters (assuming millimeters are being sent)
  var distance_in_cm = distance / 1;

  return {
    "distance": distance_in_cm
  };
}*/
```
# CIRCUIT DIAGRAM:
![WhatsApp Image 2025-11-12 at 10 13 59_39d95705](https://github.com/user-attachments/assets/4723850e-ae0e-4fa7-a7c3-11221e59121f)

# OUTPUT:
<img width="1851" height="572" alt="Screenshot 2025-11-11 114917" src="https://github.com/user-attachments/assets/fe3757ec-53c3-4c96-a04f-1c4adc27ba07" />
<img width="1919" height="1040" alt="Screenshot 2025-11-11 114809" src="https://github.com/user-attachments/assets/0b70c1f7-5383-4ab4-81b8-5aa701301c4f" />


# RESULT:

The temperature sensor was successfully interfaced with the IoT controller (Arduino), and the temperature/humidity data was accurately measured, encoded for LoRaWAN, transmitted via a LoRa module to a LoRaWAN gateway, and uploaded to the cloud for real-time distance monitoring through a cloud dashboard.
