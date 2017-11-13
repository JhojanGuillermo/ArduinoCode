#include <SoftwareSerial.h>
 // arduino Rx (pin 2) ---- ESP8266 Tx
 // arduino Tx (pin 3) ---- ESP8266 Rx
SoftwareSerial esp8266(3,2); 
boolean alreadyConnected = false;

void setup()
{
 Serial.begin(115200);  // monitor serial del arduino
 esp8266.begin(115200); // baud rate del ESP8255

 pinMode(5,OUTPUT);
 digitalWrite(5,HIGH);
 
 pinMode(6,OUTPUT);
 digitalWrite(6,HIGH);

 pinMode(7,OUTPUT);
 digitalWrite(7,HIGH);

 pinMode(8,OUTPUT);
 digitalWrite(8,HIGH);

 pinMode(9,OUTPUT);
 digitalWrite(9,HIGH);

 pinMode(10,OUTPUT);
 digitalWrite(10,HIGH);

 pinMode(11,OUTPUT);
 digitalWrite(11,HIGH);

 pinMode(12,OUTPUT);
 digitalWrite(12,HIGH);
 
 sendData("AT+RST\r\n",2000);      // resetear módulo
 sendData("AT+CWMODE=1\r\n",1000); // configurar como cliente //opcional para cuando conectar a un ip
 sendData("AT+CWJAP=\"VILCHEZ\",\"10096001\"\r\n",8000); //SSID y contraseña para unirse a red 
 sendData("AT+CIFSR\r\n",1000);    // obtener dirección IP
 sendData("AT+CIPMUX=1\r\n",1000); // configurar para multiples conexiones
 sendData("AT+CIPSERVER=1,80\r\n",1000);         // servidor en el puerto 80 //quitar para AT+CIPSTART
}
void loop()
{
  String inString = "";
  if(esp8266.available()){
    if(!alreadyConnected){
      esp8266.flush();
      Serial.println("We have a new client");
      esp8266.println("Hello, client!");
      alreadyConnected = true;
    }

    if(esp8266.find("+IPD,")){
      String tempS = "";
      esp8266.find("pin=");
      char temp = esp8266.read();
      tempS = temp;

      int pinNumber = tempS.toInt();
      if(digitalRead(pinNumber) == HIGH){
        digitalWrite(pinNumber, LOW);
        Serial.println("Encendiendo foco Nro: ");
        Serial.println(pinNumber);
      }else{
        digitalWrite(pinNumber, HIGH);
        Serial.println("Apagando foco Nro: ");
        Serial.println(pinNumber);
      }
    }
  }
}
/*
Enviar comando al esp8266 y verificar la respuesta del módulo, todo esto dentro del tiempo timeout
*/
void sendData(String comando, const int timeout)
{
 long int time = millis(); // medir el tiempo actual para verificar timeout
 
 esp8266.print(comando); // enviar el comando al ESP8266
 
 while( (time+timeout) > millis()) //mientras no haya timeout
 {
 while(esp8266.available()) //mientras haya datos por leer
 { 
 // Leer los datos disponibles
 char c = esp8266.read(); // leer el siguiente caracter
 Serial.print(c);
 }
 } 
 return;
}
