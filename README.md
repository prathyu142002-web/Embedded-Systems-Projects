#include <ESP8266WiFi.h>
#include <FirebaseArduino.h>
#include <Adafruit_Sensor.h>
#include <DHT.h>
#include <DHT_U.h>

#define DHTPIN    D4        // Pin which is connected to the DHT sensor.


#define DHTTYPE           DHT11     // DHT 11 


DHT_Unified dht(DHTPIN, DHTTYPE);

uint32_t delayMS;  

#define FIREBASE_HOST "iotvmr-c4380-default-rtdb.firebaseio.com"
#define FIREBASE_AUTH "1FeVD4UlBKTxth5QDnpKvrTx8mMjs8O2ykG9F8xF"
#define WIFI_SSID "iotproject"
#define WIFI_PASSWORD "iotproject01"

String msg;

int m11=D0;
int m12=D1;
int m21=D3;
int m22=D2;
int gas=A0;
int i,j,k,l,u,v;
int m,n,o,p;
int q,r,s,z,t;
int sensorValue = 0;
float smokePercentage = 0.0;

void setup() 
{
Serial.begin(9600);
Serial.println("DHTxx Unified Sensor Example");
 
  sensor_t sensor;
  dht.temperature().getSensor(&sensor);
  
  dht.humidity().getSensor(&sensor);
  
  delayMS = sensor.min_delay / 1000;
  
pinMode(m11,OUTPUT);
pinMode(m12,OUTPUT);
pinMode(m21,OUTPUT);
pinMode(m22,OUTPUT);
pinMode(gas,INPUT);

digitalWrite(m11,LOW);
digitalWrite(m12,LOW);
digitalWrite(m21,LOW);
digitalWrite(m22,LOW);

WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
    Serial.print("Connecting");
    while(WiFi.status() != WL_CONNECTED)
    {
        Serial.print(".");
        delay(500);
      }
      Serial.println();
      Serial.print("connected: ");
      Serial.println(WiFi.localIP());

      Firebase.begin(FIREBASE_HOST, FIREBASE_AUTH);  

  Firebase.setFloat ("ROBOIOT/STOP",s);
  s++;

}

void loop() 
{

   if(Firebase.failed())
     {
       Serial.print("Firebase error");
       Serial.println(Firebase.error());
     }

delay(delayMS);
 
  sensors_event_t event;  
  dht.temperature().getEvent(&event);
 
  
    
 int temp=event.temperature;

    Serial.print("Temperature: ");
    Serial.print(event.temperature);
    Serial.println(" *C");
    
Serial.print("temp");
Serial.println(temp);
  dht.humidity().getEvent(&event);
 
    Serial.print("Humidity: ");
    Serial.print(event.relative_humidity);
    Serial.println("%");
    if(temp>60)
    {
        Firebase.setFloat ("ROBOIOT/TEMP",30);
Firebase.setFloat ("ROBOIOT/HUMIDITY",65);
     }
     else
     {
    Firebase.setFloat ("ROBOIOT/TEMP",temp);
Firebase.setFloat ("ROBOIOT/HUMIDITY",event.relative_humidity);
     }
      sensorValue = analogRead(gas);

  // Convert the sensor value to a percentage (0-100%)
  smokePercentage = (sensorValue / 1023.0) * 100.0;
      int sen_v=analogRead(gas); 
          Serial.print("GAS:");
            Serial.println(sen_v);
             Firebase.setFloat ("ROBOIOT/GAS",sen_v);

  
msg=Firebase.getString("ROBOIOT/ROBO");



 if( (msg == "\"UP\""))
  {
digitalWrite(m11,LOW);
digitalWrite(m12,HIGH);

Serial.println("UP");
  Firebase.setFloat ("ROBOIOT/UP",i);
  i++;
  }

 if( (msg == "\"DOWN\""))
  {
digitalWrite(m11,HIGH);
digitalWrite(m12,LOW);

Serial.println("DOWN");
  Firebase.setFloat ("ROBOIOT/DOWN",i);
  i++;
  }

 if( (msg == "\"PICK\""))
  {

digitalWrite(m21,LOW);
digitalWrite(m22,HIGH);
Serial.println("PICK");
delay(1000);
digitalWrite(m11,LOW);
digitalWrite(m12,LOW);
digitalWrite(m21,LOW);
digitalWrite(m22,LOW);
Serial.println("STOP");
  Firebase.setFloat ("ROBOIOT/PICK",i);
  i++;

  }  

if( (msg == "\"place\""))
  {

digitalWrite(m21,HIGH);
digitalWrite(m22,LOW);
Serial.println("place");
delay(1000);
digitalWrite(m11,LOW);
digitalWrite(m12,LOW);
digitalWrite(m21,LOW);
digitalWrite(m22,LOW);
Serial.println("STOP");
  Firebase.setFloat ("ROBOIOT/place",i);
  i++;

  }


if((msg == "\"stop\""))
  {
digitalWrite(m11,LOW);
digitalWrite(m12,LOW);
digitalWrite(m21,LOW);
digitalWrite(m22,LOW);
Serial.println("STOP");
  Firebase.setFloat ("ROBOIOT/STOP",i);
  i++;
  }


}
