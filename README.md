# Práctica ESP32 con DHT22 usando Node Red de Diego Llampallas
Este repositorio muestra como podemos programar una ESP32 con el sensor DHT22 y ver los resultados en una página gráficados llamado node red.


## Introducción
A través de la página https://wokwi.com/  se puede hacer simulaciones de programas con arduino y sensores.
### Descripción

La ```Esp32``` la utilizamos en un entorno de adquision de datos, lo cual en esta practica ocuparemos un sensor (```DTH22```) para adquirir temperatura y humedad del entorno y a tráves de la página de node red se pueda visualizar los datos mostrados del sensor graficandolos usando gráficas interactivas que se mueven conforme sensea el sensor dht22 mostrandolos en una página web creada por node red; Cabe aclarar que esta practica se usara un simulador llamado [WOKWI](https://https://wokwi.com/).


## Material Necesario

Para realizar esta practica se usaran los siguientes elementos:

- [WOKWI](https://https://wokwi.com/)
- Tarjeta ESP 32
- Sensor DHT22
- [NODE RED](http://localhost:1880/)

# Pasos previos
# Instalar Node-Red

## Pasos para instalación

1. Entrar a la pagina  https://nodejs.org/en
2. Descargar el archivo **18.16.0 LTS** como se muestra en la siguente imagen.

![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/1.png?raw=true)

3. Abrir el archivo e instalar el programa [node.js](https://nodejs.org/en)
4. Abrir terminal en modo administrador y escribir lo siguente:
```
npm install -g --unsafe-perm node-red
```

5. Despues comprobamos que funcione node-red con el siguente codigo: (con este mismo codigo podemos arrancar el programa siempre que lo necesitemos)

```
node-red
```
 
 ## Arranque de programa

 1. Para arrancar el Programa  **Node-red** se usa el codigo :

 ```
node-red
```
2. Para abrir la aplicación nos vamos algun explorador y colocamos el siguente link:    ```localhost:1880```

## Instalación de Dashboard

1. Abrimos la pestaña de opciones y elegimos ```Manage palette``` 

![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/2.png?raw=true)

2. Seleccionamos **Install* y buscamos ```node-red-dashboard```.
3. Seleccionamos ```node-red-dashboard```.
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/3.png?raw=true)
4. Seleccionamos **Install* y buscamos ```mysql```.
5. Seleccionamos ```mysql```.


## Instrucciones

### Requisitos previos

Para poder usar este repositorio necesitas entrar a la plataforma [WOKWI](https://https://wokwi.com/).

### Instrucciones de preparación de entorno 
1. Una vez dentro de wokwi seleccionar la tarjeta ESP32

![](https://github.com/DiegoLlampallas/Practica-DHT22/blob/main/6.png?raw=true)

2. Abrir la terminal de programación y colocar la siguente programación:

## Programación

```
#include <ArduinoJson.h>
#include <WiFi.h>
#include <PubSubClient.h>
#define BUILTIN_LED 2
#include "DHTesp.h"
const int DHT_PIN = 15;
DHTesp dhtSensor;
// Update these with values suitable for your network.

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqtt_server = "44.195.202.69";
String username_mqtt="DiegoLlampallas";
String password_mqtt="12345678";

WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE  (50)
char msg[MSG_BUFFER_SIZE];
int value = 0;

void setup_wifi() {

  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  // Switch on the LED if an 1 was received as first character
  if ((char)payload[0] == '1') {
    digitalWrite(BUILTIN_LED, LOW);   
    // Turn the LED on (Note that LOW is the voltage level
    // but actually the LED is on; this is because
    // it is active low on the ESP-01)
  } else {
    digitalWrite(BUILTIN_LED, HIGH);  
    // Turn the LED off by making the voltage HIGH
  }

}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(), username_mqtt.c_str() , password_mqtt.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

void setup() {
  pinMode(BUILTIN_LED, OUTPUT);     // Initialize the BUILTIN_LED pin as an output
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);
}

void loop() {


delay(1000);
TempAndHumidity  data = dhtSensor.getTempAndHumidity();
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 2000) {
    lastMsg = now;
    //++value;
    //snprintf (msg, MSG_BUFFER_SIZE, "hello world #%ld", value);

    StaticJsonDocument<128> doc;

    doc["DEVICE"] = "ESP32";
    //doc["Anho"] = 2022;
    //doc["Empresa"] = "Educatronicos";
    doc["TEMPERATURA"] = String(data.temperature, 1);
    doc["HUMEDAD"] = String(data.humidity, 1);
   

    String output;
    
    serializeJson(doc, output);

    Serial.print("Publish message: ");
    Serial.println(output);
    Serial.println(output.c_str());
    client.publish("Diego/Alberto", output.c_str());
  }
}

```


## Partes
1. ![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/4.png?raw=true)



## Librerias
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/5.png?raw=true)

## Conexión

![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/6.png?raw=true)

# Configuración y conexión de node red

## Conexión de node red
 1. Colocar bloque ```mqqtt in```.
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/8.png?raw=true)
 2. Colocar bloque ```json```.
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/9.png?raw=true)
 3. Colocar 2 bloques ```function```.
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/10.png?raw=true)
 4. Colocar 2 bloques ```gauge```.
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/11.png?raw=true)
 5. Colocar bloque ```chart```.
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/12.png?raw=true)
 6. Conectamos todos los componentes como se muestra en la imagen:
![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/13.png?raw=true)

Tras conectar todo se pasa a la configuración

## Configuración de node red


### Instrucciónes de operación

1. Iniciar simulador.
2. Visualizar los datos en el monitor serial.
3. Colocar la temperatura y humedad dando *doble click* al sensor **DHT11** 


## Resultados

Cuando haya funcionado, verás los valores dentro del monitor serial.
## Funcionamiento

![](https://github.com/DiegoLlampallas/DHT22NODERED/blob/main/7.png?raw=true)
## Evidencias

[Página](https://wokwi.com/projects/367749529453942785)


# Créditos

Desarrollado por Ing. Diego Alberto Llampallas Vega

- [GitHub](https://github.com/DiegoLlampallas)