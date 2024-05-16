//Checking water levels and streaming sensor data through nodemcu

//Arduino Code
//8 4 and 2 is on relay

#define control3 9 //D2 conected to 9 pin of arduino.
#define control2 10//D0 conected to 10 pin of arduino.
#define control1 12//D1 conected to 12 pin of arduino.

#define relay1 8 //8 conected to peltier 1.
#define relay2 4//4 conected to peltier2.
#define relay3 2//2 conected to peltier3.

#define WaterLevel1 3 //D5 conected to 3 pin of arduino.
#define WaterLevel2 5//D6 conected to 5 pin of arduino.
#define WaterLevel3 7//D7 conected to 7 pin of arduino.


void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(control3, INPUT);
  pinMode(control2, INPUT);
  pinMode(control1, INPUT);

  pinMode(relay3, OUTPUT);
  pinMode(relay2, OUTPUT);
  pinMode(relay1, OUTPUT);

  pinMode(WaterLevel3, OUTPUT);
  pinMode(WaterLevel2, OUTPUT);
  pinMode(WaterLevel1, OUTPUT);

  Serial.begin(9600);
}

// the loop function runs over and over again forever
void loop() {
  
 ////Water Level Code
  int WaterSen2 = analogRead(A0); // Short wire lenght A0 water sensor
  // print out the value you read:
  //Serial.println(sensorValue);
  delay(50);        // delay in between reads for stability

    int WaterSen1 = analogRead(A5);// Long wire lenght A5 water sensor
  // print out the value you read:
 // Serial.println(WaterSen2);
  delay(50);        // delay in between reads for stability

  if(WaterSen1<500 && WaterSen2<500)
  {
    Serial.println("Water is very low or empty");
    digitalWrite(WaterLevel1,HIGH);
    digitalWrite(WaterLevel2,LOW);
    digitalWrite(WaterLevel3,LOW);
    }

   else if(WaterSen1>500 && WaterSen2<500)
  {
    Serial.println("Water is at medium level");
    digitalWrite(WaterLevel1,HIGH);
    digitalWrite(WaterLevel2,HIGH);
    digitalWrite(WaterLevel3,LOW);
    }

       else if(WaterSen1>500 && WaterSen2>500)
  {
    Serial.println("Water is at High level");
    digitalWrite(WaterLevel1,HIGH);
    digitalWrite(WaterLevel2,HIGH);
    digitalWrite(WaterLevel3,HIGH);
    }
   
   else
   {
    Serial.println("Nothing water level");
    digitalWrite(WaterLevel1,LOW);
    digitalWrite(WaterLevel2,LOW);
    digitalWrite(WaterLevel3,LOW);
    }



/// Controlling Code
 int val1= digitalRead(control1);
 Serial.println("val1");
 Serial.println(val1);

 if(val1==1)
 {
  digitalWrite(relay1,HIGH); //Relay gets ON on low.. Relay OFF on high
  }

  else
  {
   digitalWrite(relay1,LOW); //Relay gets ON on low.. Relay OFF on high 
    }

  int val2= digitalRead(control2);
 Serial.println("val2");
 Serial.println(val2);

  if(val2==1)
 {
  digitalWrite(relay2,HIGH);//Relay gets ON on low.. Relay OFF on high
  }

  else
  {
   digitalWrite(relay2,LOW); //Relay gets ON on low.. Relay OFF on high
    }
 
  int val3= digitalRead(control3);
 Serial.println("val3");
 Serial.println(val3);
  if(val3==1)
 {
  digitalWrite(relay3,HIGH);  //Relay gets ON on low.. Relay OFF on high
  }

  else
  {
   digitalWrite(relay3,LOW); //Relay gets ON on low.. Relay OFF on high
    }

delay(50);
}

/////////////////////////////////////////////////

#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>

// Water Status Inputs
#define WaterLev1 D5 //D5 conected to 3 pin of arduino.
#define WaterLev2 D6 //D6 conected to 5 pin of arduino.
#define WaterLev3 D7 //D7 conected to 7 pin of arduino.

    #define LED     D0        // Led in NodeMCU at pin GPIO16 (D0)
     
    const char* ssid     = "Fouzan1";
    const char* password = "90167b7e";     

    int wifiStatus;
     
    void setup() {
        // W
      pinMode(WaterLev1, INPUT);
      pinMode(WaterLev2, INPUT);
      pinMode(WaterLev3, INPUT);
      
      Serial.begin(115200); // Serial Monitor should also have same baud rate matching. Baud rate= number of bits per second.
      delay(200);
      
     
     pinMode(LED,OUTPUT);
     
      // We start by connecting to a WiFi network
   
      Serial.print("Your are connecting to;");
      Serial.println(ssid);

  //making a static ip of esp
  //Static IP address configuration
  //IPAddress staticIP(192, 168, 1, 50); //ESP static ip
  //IPAddress gateway(192, 168, 1, 1);   //IP Address of your WiFi Router (Gateway)
 // IPAddress subnet(255, 255, 255, 0);  //Subnet mask
 // IPAddress dns(8, 8, 8, 8);  //DNS
  
 // WiFi.config(staticIP, subnet, gateway, dns);
      
      WiFi.begin(ssid, password);
      
      while (WiFi.status() != WL_CONNECTED) {
        delay(500); //.5second delsy
        Serial.print(".");
      }
     
 }   
     
void loop() {
      WiFiClient client;
      wifiStatus = WiFi.status();

      if(wifiStatus == WL_CONNECTED){
         Serial.println("");
         Serial.println("Your ESP is connected!");  
         Serial.println("Your IP address is: ");
         Serial.println(WiFi.localIP()); 
         
         HTTPClient http,http2;    //Declare object of class HTTPClient
         
      
         http.begin("http://192.168.1.14/ResponseFile.php");      //Specify request destination
         http.addHeader("Content-Type", "application/x-www-form-urlencoded");  //Specify content-type header
      
         int httpCode = http.POST("temp=20&light=88");   //Send the request
         String payload = http.getString();                  //Get the response payload
      
         Serial.println(httpCode);   //Print HTTP return code
         Serial.println(payload);    //Print request response payload

         if(payload.toInt()== 0)
         {
          Serial.println("Light to on");
          digitalWrite(LED,HIGH);
          }
         else{
          Serial.println("Light to off");
          digitalWrite(LED,LOW);
          }

          


    

          int Water1=digitalRead(WaterLev1);   // turn the LED on (HIGH is the voltage level)
          int Water2=digitalRead(WaterLev2);
          int Water3=digitalRead(WaterLev3);

          if(Water1==HIGH && Water2==LOW && Water3==LOW)
          {
            Serial.println("Water is very low or empty");
         
         http2.begin("http://192.168.1.14/updatezero.php");      //Specify request destination
         http2.addHeader("Content-Type", "application/x-www-form-urlencoded");  //Specify content-type header

         http2.begin("http://192.168.1.14/updatezero.php");      //Specify request destination
         http2.addHeader("Content-Type", "application/x-www-form-urlencoded");  //Specify content-type header
      
         int httpCode2 = http2.POST("temp=20&light=88");   //Send the request
         String payload2 = http2.getString();                  //Get the response payload
      
         Serial.println(httpCode2);   //Print HTTP return code
         Serial.println(payload2);    //Print request response payload

            }

          else if(Water1==HIGH && Water2==HIGH && Water3==LOW)
          {
            Serial.println("Water is medium level");
         http2.begin("http://192.168.1.14/updateone.php");      //Specify request destination
         http2.addHeader("Content-Type", "application/x-www-form-urlencoded");  //Specify content-type header      

         http2.begin("http://192.168.1.14/updateone.php");      //Specify request destination
         http2.addHeader("Content-Type", "application/x-www-form-urlencoded");  //Specify content-type header      

         int httpCode3 = http2.POST("temp=20&light=88");   //Send the request
         String payload3 = http2.getString();                  //Get the response payload
      
         Serial.println(httpCode3);   //Print HTTP return code
         Serial.println(payload3);    //Print request response payload

            }

          else if(Water1==HIGH && Water2==HIGH && Water3==HIGH)
          {
            Serial.println("Water is high level");
         http2.begin("http://192.168.1.14/updatehundred.php");      //Specify request destination
         http2.addHeader("Content-Type", "application/x-www-form-urlencoded");  //Specify content-type header  

         http2.begin("http://192.168.1.14/updatehundred.php");      //Specify request destination
         http2.addHeader("Content-Type", "application/x-www-form-urlencoded");  //Specify content-type header

         int httpCode4 = http2.POST("temp=20&light=88");   //Send the request
         String payload4 = http2.getString();                  //Get the response payload
      
         Serial.println(httpCode4);   //Print HTTP return code
         Serial.println(payload4);    //Print request response payload

 
            }
          else
          {
             Serial.println("Nothing water level");
             
          }
          http.end();  //Close connection
          http2.end();  //Close connection
      
        
      }
      else{
        Serial.println("");
        Serial.println("WiFi not connected");
      }
      delay(500); // check for connection every once a second

}