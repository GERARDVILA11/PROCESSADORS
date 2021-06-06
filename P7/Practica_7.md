# Pràctica 7 : Busos de comunicació III (I2S)
## 7.1 Reproducció des de memòria interna
### **Codi**

~~~
#include <Arduino.h>
#include <SPIFFS.h>
#include <HTTPClient.h>
#include <AudioGeneratorAAC.h>
#include <AudioOutputI2S.h>
#include <AudioFileSourcePROGMEM.h>
#include <sampleaac.h>
AudioFileSourcePROGMEM *in;
AudioGeneratorAAC *aac;
AudioOutputI2S *out;
void setup(){
Serial.begin(115200);
in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac));
aac = new AudioGeneratorAAC();
out = new AudioOutputI2S();
out -> SetGain(0.125);
out -> SetPinout(26,25,22);
aac->begin(in, out);
}
void loop(){
if (aac->isRunning()) {
aac->loop();
} else {
aac -> stop();
Serial.printf("Sound Generator\n");
delay(1000);
}
}
~~~

### **Funcionament**

L'objectiu d'aquesta primer part de la pràctica es reproduir música des de un arxiu que està emmagatzemat a la memòria interna(RAM) del microprocesador.

Per aquesta pràctica necesitarem les llibreries d'audio ESP8266Audio com que nomès el ESP32 i el ESP8266 admeten el protocol I2S ens anirà perfecte per dur a terme el programa.

Hem de saber que les dades del so digital s'emmagatzemen al fitxer *"sampleaac.h*, és important que Arduino (fitxer .ino) i la capçalera (fitxer .h) es trobin a la mateixa carpeta.

Després d’incloure els fitxers de capçalera de la biblioteca ESP8266Audio, donem als tres primers una variable curta, que conté funcions. 

En el *void setup* establim la velocitat de transmissió en 115200 (la qual l'hem configurat ja en el *platformio.ini* )i inicialitzem els fitxers de capçalera. Per a *AudioFileSourcePROGMEM*, definim que el fitxer d’àudio es troba al fitxer *sampleaac* aquest es guarda en forma de matriu.

L'objecte *AudioOutputI2S* té diferents funcions:
- *SetGain* : Redueix el volum de l'altaveu.
- *Pinout* : Defineix el pin de sortida.

L'últim pas és connectar les dades de so d'entrada de la memòria interna del programa a la sortida d'àudio I2S amb la funció d'inici *AudioGeneratorAAC*.

Al *void loop*, el generador d'àudio continua funcionant fins que tota la matriu de so s'executa a través del generador. Quan s'acaba, el  generador deixa de funcionar i a la sortida sèrie podem veure que el generador de so està acabat.

## 7.2 Reproducció d'un  fitxer WAVE des de una tarjeta SD.
### **Codi**
~~~
#include <Arduino.h>
#include <Audio.h>
#include <SD.h>
#include <FS.h>

// Digital I/O used
#define SD_CS 5
#define SPI_MOSI 23
#define SPI_MISO 19
#define SPI_SCK 18
#define I2S_DOUT 25
#define I2S_BCLK 27
#define I2S_LRC 26

Audio audio;
void setup(){
pinMode(SD_CS, OUTPUT);
digitalWrite(SD_CS, HIGH);
SPI.begin(SPI_SCK, SPI_MISO, SPI_MOSI);
Serial.begin(115200);
SD.begin(SD_CS);
audio.setPinout(I2S_BCLK, I2S_LRC, I2S_DOUT);
audio.setVolume(10); // 0...21
audio.connecttoFS(SD, "Ensoniq-ZR-76-01-Dope-77.wav");
}
void loop(){
audio.loop();
}
~~~
### **Funcionament**

A la primera part de l'script incloem totes les llibreries i definim els pins que s'utilitzen per connectar l'ESP32 al I2S i al mòdul de la targeta SD.

Després d'inicialitzar l'objecte Àudio amb el nom *àudio*, es crida el *void setup*.
 En el *void setup*, es defineixen els pins i la connexió SPI per a la comunicació de la targeta SD. La velocitat de transmissió s'estableix en 115200 i també s'inicialitza l'objecte de la targeta SD.Per a l'objecte d'àudio, els pins anteriors es configuren al pinout i reduïm el volum de so a 10. Per últim s'han de connectar les entrades i sortides, per tant, connectem l’objecte d’àudio amb l’objecte de la targeta SD i definim l'adrça del fitxer WAVE. 

Al *void loop* només hem de fer un bucle sobre l’objecte d’àudio preconfigurat per reproduir la música.



