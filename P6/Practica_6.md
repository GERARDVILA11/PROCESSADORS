# Pràctica 6: Busos de comunicació II (SPI)
## 6.1 Lectura/ escriptura de memòria SD
### **Codi**
~~~
#include <SPI.h>
#include <SD.h>
File myFile;
void setup()
{
Serial.begin(9600);
Serial.print("Iniciando SD ...");
if (!SD.begin(4)) {
Serial.println("No se pudo inicializar");
return;
}
Serial.println("inicializacion exitosa");
myFile = SD.open("archivo.txt");//abrimos el archivo
if (myFile) {
Serial.println("archivo.txt:");
while (myFile.available()) {
Serial.write(myFile.read());
}
myFile.close(); //cerramos el archivo
} else {
Serial.println("Error al abrir el archivo");
}
}
void loop()
{
}
~~~

## **Funcionament**

Primer de tot hem d'agregar les llibreries necesàries, en aquest cas en necesitem dos, la primera per connectar-nos al bus SPI(*#include<SPI.h>*) i la segona per dir-li al programa que treballarem amb una tarjeta SD (*#include<SD.h>*).

Un cop agregades les llibreries creem una variable anomenada *myfile* i de tipus *file* la qual serà el fitxer que llegirem/escriurem de la tarjeta SD.

En el *void setup* fem varies coses; la primera inicialitzar la tarjeta SD, si aquesta no s'ha inicilitzat el programa ens dona un missatge d'error, en el cas contrari escriu per port sèrie: "Inicializacion exitosa".

Seguidament obrim el fitxer *myfile*, creem un bucle amb el nom del fitxer que tenim dins la tarjeta, en el nostre cas es un arxiu .txt anomenat "archivo.txt". Si aquest esta disponible el programa el llegeix i l'escriu per pantalla, posteriorment tanca l'arxiu.
Si el fitxer no s'ha pogut obrir, el programa ens dona un missatge d'error.

En aquest cas en el *void loop* no fem res i el podem deixar buit.

## 6.2 Lectura d'etiqueta RFID
### **Codi**
~~~
#include <SPI.h>
#include <MFRC522.h>
#define RST_PIN 9 
#define SS_PIN 10 
MFRC522 mfrc522(SS_PIN, RST_PIN); //Creamos el objeto para el RC522
void setup() {
Serial.begin(9600); 
SPI.begin(); 
mfrc522.PCD_Init(); 
Serial.println("Lectura del UID");
}
void loop() {
// Revisamos si hay nuevas tarjetas presentes
if ( mfrc522.PICC_IsNewCardPresent())
{
//Seleccionamos una tarjeta
if ( mfrc522.PICC_ReadCardSerial())
{
// Enviamos serialemente su UID
Serial.print("Card UID:");
for (byte i = 0; i < mfrc522.uid.size; i++) {
Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
Serial.print(mfrc522.uid.uidByte[i], HEX);
}
Serial.println();
// Terminamos la lectura de la tarjeta actual
mfrc522.PICC_HaltA();
}
}
~~~
### **Funcionament**

En aquest apartat necesitem la llibreria *#include <SPI.h>* i apart hem d'incloure la llibreria del mòdul RC522 *#include <MFRC522.h>*.

Un cop tenim les llibreries incloses definim els pins. El pin 9 l'utilitzarem per resetjar el mòdul RC522 i el pin 10 per el SS(SDA) del mòdul, seguidament creem l'objecte del mòdul ho fem amb la funció *MFRC522 mfrc522(SS_PIN, RST_PIN);*.

En el *void setup* iniciem la comunicació del serial, el bus SPI i el mòdul MFRC522. Un cop tenim tot inicialitzat el programa escriu per pantalla: "Lectura del UID". 

En el *void loop* revisem si hi ha alguna tarjeta present, si es així seleccionem la tarjeta,llegim el seu serial i imprimim el UID per pantalla.
