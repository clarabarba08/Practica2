# PRÀCTICA 2: Interrupcions
<div align=right> 

Clara Barba Armengol
<div>

<div align="justify">

En aquesta pràctica hem treballat el funcionament de les interrupcions. Controlarem 2 leds de forma periòdica, i tindrem una entrada (un botó) que provocarà un canvi de freqüència de les oscilacions d'un dels leds.
<div>

## MATERIAL
- 2 leds
- 2 resistències de 330Ohm
- Cables
- ESP32

## PRÀCTICA A
<div align="justify">

Tenim dos leds que estan encesos, però el apretar un botó un dels leds s'apaga durant 500ms. 
La següent instrucció estableix un pin d'interrupció, el nom de la funció que crida cada cop que es dispari la interrupció i quan s'ha de disparar. En aquest cas *falling* indica que la interrupció succeeix quan s'apreta el botó.
```cpp
attachInterrupt(button1.PIN, isr, FALLING);
```
La funció que cridem passa a *true* la variable *button1.pressed* i conta els cops que ha estat apretat el botó. En el *loop* tenim un condicional que quan detecta que el botó està apretat apaga un dels leds durant 500ms, i mostra per pantalla el número de vegades que ha estat apretat el botó. 
```cpp
if (button1.pressed) {
Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
digitalWrite(ledgroc,LOW);
delay(500);
button1.pressed = false;
}
```
En el següent vídeo podem veure el funcionament d'aquest muntatge. 

[Vídeo pràctica 2a1](https://drive.google.com/file/d/1S7SWgX8qRtnsj2cJUopEu4GAZfXp5X87/view?usp=sharing)

En aquest mateix codi també hi ha una itnerrupció per Timer (event programat). És a dir la interrupció té lloc després d'un temps programat. 
```cpp
//Detach Interrupt after 1 Minute
static uint32_t lastMillis = 0;
if (millis() - lastMillis > 60000) {
lastMillis = millis();
detachInterrupt(button1.PIN);
Serial.println("Interrupt Detached!");
}
```
En aquest cas després d'un minut es desactiven les interrupcions del pin. És a dir ens apareix per pantalla "Interrupt Detached!" i quan s'apreta el botó ja no s'apaga el led. Ho podem veure en el següent vídeo: [Vídeo pràctica 2a2](https://drive.google.com/file/d/1tfPykJPB5cBUJFt30dK-nheHBcGurHuP/view?usp=sharing)

<div>

## PRÀCTICA B: Interrupció per Timer

Ara tenim un bucle que detecta cada cop qui hi ha hagut una interrupció i va mostrant per pantalla quantes interrupcions porta. Les interrupcions ocorren periòdicament sense fi segons un timer prèviament programat. El codi és el següent: 
```cpp
volatile int interruptCounter;
int totalInterruptCounter;
hw_timer_t * timer = NULL;
portMUX_TYPE timerMux = portMUX_INITIALIZER_UNLOCKED;
void IRAM_ATTR onTimer() {

portENTER_CRITICAL_ISR(&timerMux);
interruptCounter++;
portEXIT_CRITICAL_ISR(&timerMux);
}
void setup() {
Serial.begin(115200);
timer = timerBegin(0, 80, true);
timerAttachInterrupt(timer, &onTimer, true);
timerAlarmWrite(timer, 1000000, true);
timerAlarmEnable(timer);
}
void loop() {
if (interruptCounter > 0) {
portENTER_CRITICAL(&timerMux);
interruptCounter--;
portEXIT_CRITICAL(&timerMux);
totalInterruptCounter++;
Serial.print("An interrupt as occurred. Total number: ");
Serial.println(totalInterruptCounter);
}
}
```
En el vídeo podem veure el que es va mostrant per pantalla. 

[Vídeo pràctica 2b](https://drive.google.com/file/d/1VqgiWp01jgEF_e1Xm_8I2XYmw5j-lY5i/view?usp=sharing)