# Práctica ESP32 con ULTRASONICO con DHT22 de Diego Llampallas
Este repositorio muestra como podemos programar una ESP32 con el sensor DHT11 y un sensor ultrasonico y ver los resultados con la pantalla LCD 16X2.


## Introducción
A través de la página https://wokwi.com/  se puede hacer simulaciones de programas con arduino y sensores.
### Descripción

La ```Esp32``` la utilizamos en un entorno de adquision de datos, lo cual en esta practica ocuparemos un sensor (```DTH11```) para adquirir temperatura y humedad del entorno y de paso medir distancia con sensor ultrasonico mostrando resultados en pantalla LCD 16X2; Cabe aclarar que esta practica se usara un simulador llamado [WOKWI](https://https://wokwi.com/).


## Material Necesario

Para realizar esta practica se usaran los siguientes elementos:

- [WOKWI](https://https://wokwi.com/)
- Tarjeta ESP 32
- Sensor DHT11
- Pantalla LCD 16x2
- Sensor ultrasonico
- Fuente de Voltaje 5v
- Tierra



## Instrucciones

### Requisitos previos

Para poder usar este repositorio necesitas entrar a la plataforma [WOKWI](https://https://wokwi.com/).


### Instrucciones de preparación de entorno 
1. Una vez dentro de wokwi seleccionar la tarjeta ESP32

![](https://github.com/DiegoLlampallas/Practica-DHT22/blob/main/6.png?raw=true)

2. Abrir la terminal de programación y colocar la siguente programación:

## Programación

```

#include <LiquidCrystal_I2C.h>
#include "DHTesp.h"
#define I2C_ADDR    0x27
#define LCD_COLUMNS 20
#define LCD_LINES   4

const int Trigger = 14;   //Pin digital 2 para el Trigger del sensor
const int Echo = 26;   //Pin digital 3 para el Echo del sensor

const int DHT_PIN = 15;

DHTesp dhtSensor;

LiquidCrystal_I2C lcd(I2C_ADDR, LCD_COLUMNS, LCD_LINES);
void setup() {
 Serial.begin(9600);
  pinMode(Trigger, OUTPUT); //pin como salida
  pinMode(Echo, INPUT);  //pin como entrada
  digitalWrite(Trigger, LOW);//Inicializamos el pin con 0
    dhtSensor.setup(DHT_PIN, DHTesp::DHT22);
    lcd.init();
  lcd.backlight();
}

void loop()
{

  long t; //timepo que demora en llegar el eco
  long d; //distancia en centimetros

  digitalWrite(Trigger, HIGH);
  delayMicroseconds(10);          //Enviamos un pulso de 10us
  digitalWrite(Trigger, LOW);
  
  t = pulseIn(Echo, HIGH); //obtenemos el ancho del pulso
  d = t/59;             //escalamos el tiempo a una distancia en cm
  
  Serial.print("Distancia: ");
  Serial.print(d);      //Enviamos serialmente el valor de la distancia
  Serial.print(" Cm");
  Serial.println();
  delay(1000);          //Hacemos una pausa de 100ms

  TempAndHumidity  data = dhtSensor.getTempAndHumidity();
  Serial.println("Temp: " + String(data.temperature, 1) + "°C");
  Serial.println("Humidity: " + String(data.humidity, 1) + "%");
  Serial.println("---");
   delay(1000); 

  lcd.setCursor(0, 0);
  lcd.print("Distancia: " + String(d) +"Cm ");
   lcd.setCursor(0, 1);
  lcd.print("Diego Llampallas");
  delay(1000);          //Hacemos una pausa de 100ms
  
  lcd.setCursor(0, 0);
  lcd.println("  Temp: " + String(data.temperature, 1) + " C ");
  lcd.setCursor(0, 1);
  lcd.println("  Humidity: " + String(data.humidity, 1) + "%");
  lcd.println("---");
   delay(1000);  
}

```
![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/23.png?raw=true)

## Partes
1. ![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/24.png?raw=true)
2. ![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/25.png?raw=true)
3. ![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/26.png?raw=true)


## Librerias
![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/22.png?raw=true)

## Conexión

![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/27.png?raw=true)

### Instrucciónes de operación

1. Iniciar simulador.
2. Visualizar los datos en el monitor serial y pantalla LCD 16X2.
3. Colocar la temperatura y humedad dando *doble click* al sensor **DHT11** 
4. Medir distancia con sensor ultrasonico.

## Resultados

Cuando haya funcionado, verás los valores dentro del monitor serial y pantalla LCD 16X2.
## Funcionamiento

![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/28.png?raw=true)
![](https://github.com/DiegoLlampallas/ULTRASONICODHT22/blob/main/29.png?raw=true)
## Evidencias

[Página](https://wokwi.com/projects/367175632371544065)


# Créditos

Desarrollado por Ing. Diego Alberto Llampallas Vega

- [GitHub](https://github.com/DiegoLlampallas)