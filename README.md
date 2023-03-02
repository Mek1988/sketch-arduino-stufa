#include <OneWire.h> // libreria per la gestione del sensore di temperatura
#include <DallasTemperature.h> // libreria per la gestione del sensore di temperatura

#define ONE_WIRE_BUS 2 // definiamo il pin del sensore di temperatura
#define PIN_12 12 // definiamo il pin di uscita per il controllo 1
#define PIN_13 13 // definiamo il pin di uscita per il controllo 2

OneWire oneWire(ONE_WIRE_BUS); // creiamo un oggetto OneWire per comunicare con il sensore
DallasTemperature sensors(&oneWire); // creiamo un oggetto DallasTemperature per leggere la temperatura

int tempMax = 60; // temperatura massima
int tempMin = 40; // temperatura minima
bool controllo1 = false; // variabile per il controllo 1
bool controllo2 = false; // variabile per il controllo 2

void setup() {
  pinMode(PIN_12, OUTPUT); // impostiamo il pin 12 come uscita
  pinMode(PIN_13, OUTPUT); // impostiamo il pin 13 come uscita
  digitalWrite(PIN_12, LOW); // impostiamo lo stato iniziale del pin 12 a LOW
  digitalWrite(PIN_13, LOW); // impostiamo lo stato iniziale del pin 13 a LOW
  Serial.begin(9600); // inizializziamo la comunicazione seriale
  sensors.begin(); // inizializziamo il sensore di temperatura
}

void loop() {
  sensors.requestTemperatures(); // richiediamo la temperatura al sensore
  float temp = sensors.getTempCByIndex(0); // leggiamo la temperatura

  Serial.print("Temperatura: "); // stampiamo la temperatura sul monitor seriale
  Serial.print(temp);
  Serial.println("°C");

  if (temp >= tempMax && controllo1 == false) { // se la temperatura supera il valore massimo e il controllo 1 non è ancora stato effettuato
    digitalWrite(PIN_13, HIGH); // impostiamo il pin 13 a HIGH
    digitalWrite(PIN_12, HIGH); // impostiamo il pin 12 a HIGH
    delay(5000); // attendiamo 5 secondi
    digitalWrite(PIN_13, LOW); // impostiamo il pin 13 a LOW
    controllo1 = true; // impostiamo il controllo 1 a true
  }

  if (temp <= tempMin && controllo2 == false && controllo1 == true) { // se la temperatura scende al di sotto del valore minimo e il controllo 2 non è ancora stato effettuato e il controllo 1 è stato effettuato
    digitalWrite(PIN_13, HIGH); // impostiamo il pin 13 a HIGH
    delay(5000); // attendiamo 5 secondi
    digitalWrite(PIN_13, LOW); // impostiamo il pin 13 a LOW
    digitalWrite(PIN_12, LOW); // impostiamo il pin 12 a LOW
    controllo2 = true; // impostiamo il controllo 2
  }


  delay(1000); // attesa di 1 secondo
}
