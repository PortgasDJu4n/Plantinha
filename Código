#include "DHT.h"
/****
 WIFI_SSID "CR7"
 WIFI_PASSWORD "40028922"
 BOT_TOKEN "5705833096:AAH-brZCcae5CIoUOzH-EIaJg_nDcWE6ums"
CHAT_ID "5282815891"
*****/

#define DHT11PIN  18
DHT dht(DHT11PIN, DHT11);

#define THINGER_SERIAL_DEBUG
#include <ThingerESP32.h>
ThingerESP32 thing("DiogoRangel", "Plantinha32", "XrAENX#6$vG&0NO+");
 
//Declarando variáveis dos sensores:
int i = 0;
int Porcento;
int umidade_solo;
float umi_ar;
int temp;
int flag = 1;
char estado = 'n';

void setup()
{
pinMode(21, OUTPUT);
Serial.begin(115200);

  thing.add_wifi("CR7", "40028922"); // Configurações da internet.
   
dht.begin();

thing["dados"] >> [](pson& out)
{
  out ["temperatura"] = temp;
  out ["Umidade"] = umi_ar;
  out ["Umidade do Solo"] = Porcento;
};
}
 
void loop()
{
thing.handle();

//endpoint solo seco 
unsigned long lastCheck = 0;
unsigned long currentTs = millis();


//Checagem a cada 4 horas
if(currentTs-lastCheck >= 60*60*1000*4)
{
if(Porcento < 20)
 {
   thing.call_endpoint("BotPlantinha");
 }
}

if(currentTs-lastCheck >= 60*60*1000*4)
{
if(Porcento > 80 )
 {
   thing.call_endpoint("BOTPLANTINHAOFF");
 }
}  

//endpoint solo molhado  
if(currentTs-lastCheck >= 60*60*1000*4)
{
if(Porcento < 80 && Porcento > 40)
 {
   thing.call_endpoint("BOTPLANTINHAOK");
 }
}

//temperatura alta
if(currentTs-lastCheck >= 60*60*1000*4)
{
if(dht.readTemperature() > 40)
{
   thing.call_endpoint("TEMPERATURAQUENTE");
}
  }


//Alerta de mudança de estado
if(Porcento < 20) {estado = 's';}

 if(Porcento > 80 ){estado = 'a';}

if(Porcento < 80 && Porcento > 40){estado = 'b';}


switch(estado)
{
  case 's': thing.call_endpoint("BotPlantinha"); break;
  case 'a':thing.call_endpoint("BOTPLANTINHAOFF");break;
  case 'b': thing.call_endpoint("BOTPLANTINHAOK");break;
}


//Mapeamentos do sensores:
umidade_solo = analogRead(34);
Porcento = map(umidade_solo, 2030,4090, 100, 0);
umi_ar = dht.readHumidity();
temp = dht.readTemperature();

//DHT-11 - MONITOR SERIAL
Serial.print("Temperature: ");
Serial.print(temp);
Serial.println(" ºC");
Serial.print("Humidity: ");
Serial.print(umi_ar);
Serial.println("%");
delay(1000);

//higrometro - MONITOR SERIAL
 Serial.print("Higrômetro");
 Serial.println("  ");
 Serial.print("Umidade do Solo = ");
 Serial.print(Porcento);
 Serial.println("%");
 Serial.println("  ");



//VALVULA ON
if(Porcento < 50 || umi_ar < 40)
{
  digitalWrite(21,HIGH);
  delay(500);
}



//VALVULA OFF
if(Porcento > 50 || umi_ar > 40)
{ 
  digitalWrite(21,LOW);
}


}
