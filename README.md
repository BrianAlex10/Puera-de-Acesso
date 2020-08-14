# UNIVERSIDAD POLITÉCNICA DE TULANCINGO
  * Electronica y Telecomunicaciones  🛰
# Puerta-de-Acesso 🚪
 * Abrir la puerta con estilo 
Por que seguimos usando las puertas convencionales, !si tenemos la tecnologia al alcance de todos! 💻
Cerrar la puerta desde la comodidad de nuestro equipo celular cuando estemos dentro de cierto rango.
Olvidaste tu celular, no hay problema puedes ingresar un codigo en el teclado para ingresar.
O que tal si mejor te gusta usar una tarjeta de acceso.
# Mision 🏳
Brindar seguridad a nuestros clientes para ingresar a su hogar u oficina de trabajo. 🏠
# Tenemos una vision diferente 🚩
Como te gustaria ingresar a tu casa u oficina. 🕋
Codigo en teclado, mediante tu celular (Bluetooth) o con una tarjeta de acceso. ⚙
# ¿Por qué lo elegimos?
*****
# Software 🌎
  * Arduino IDE
# Materiales 🕹
  * Arduino UNO
  * Cable
  * Resistencia
  * Pasadorde puerta(Chapa)
  * Servomotor
  * Diseño de puerta
# Integrantes 🗿
  * Brian Escamilla Torres   IET 9no.
  * Enrique Diego Castro   IET 9no.

#PROGRAMA 🖥

//The TRIG and ECHO pins of the ultrasonic are defined and
//servoRightT & servoLeftT objects are created to control the servos
Ultrasonic ultrasonicT(6, 7);
Servo servoRightT;
Servo servoLeftT;

//Assigning the pins to the LEDs
const uint8_t ledOpenT = 2;
const uint8_t ledClosedT = 4;

//Variables to store positions of the servos, the distance of the ultrasonic, a flag that
//allows to rectify the change of distance and starts a variable of minimum seconds to two
int16_t posRightT, posLeftT, distanceT, initialDistanceT, continuousSecondsT = 0;
bool flagT = false;
const uint8_t minimalSecondsT = 2;

//loopTimeT and waitingDoorClosingT define the time (in milliseconds) that the events of
//your function will last, timeElapsedT and timeElapsedDoorClosingT are variables
//that will store the elapsed time
const uint8_t loopTimeT = 200;
unsigned long timeElapsedT = 0;
const uint16_t waitingDoorClosingT = 1000;
unsigned long timeElapsedDoorClosingT = 0;

void setup() {
  //The pins of the servos are defined
  servoLeftT.attach(3);
  servoRightT.attach(5);
  //The servos, initially, will move 90 degrees
  servoLeftT.write(90);
  servoRightT.write(90);
  pinMode(ledOpenT, OUTPUT);
  pinMode(ledClosedT, OUTPUT);
  digitalWrite(ledClosedT, HIGH);
  //The variables invoke the millis() action
  timeElapsedT = millis();
  timeElapsedDoorClosingT = millis();
}

void loop() {
  //A variable that calls the millis() function is created, then the function overflow is managed
  unsigned long currentMillisLoopT = millis();
  if ((unsigned long)(currentMillisLoopT - timeElapsedT) >= loopTimeT) {
    //The distance is obtained in real time and stored in distanceT to be compared with
    //the return value sumary(), if both distances are equal and the flag is true then
    //the door is opened, otherwise, call the function beforeCloseDoor()
    distanceT = ultrasonicT.Ranging(CM);
    if (distanceT == summary()) {
      if (distanceT < 10 && flagT == false)
        openDoor();
      else if (distanceT >= 10 && flagT == true)
        beforeCloseDoor();
    }
    timeElapsedT = millis();
  }
}

//This function returns the average of 4 readings of the distance, its purpose is to have
//a more accurate data of the measurement
uint8_t summary() {
  uint8_t sumT = 0;
  for (uint8_t iT = 0; iT < 3; iT ++) {
    sumT = sumT + (distanceT = ultrasonicT.Ranging(CM));
    delay(50);
  }
  initialDistanceT = sumT / 3;
  return (initialDistanceT);
}

//openDoor() generates 2 events, one is in the servomotors to change its position
//(both in opposite way) and another event changes the state of the LEDs
void openDoor() {
  flagT = true;
  posLeftT = 90;
  for (posRightT = 90; posRightT >= 0; posRightT -= 1) {
    if (posLeftT <= 180) {
      posLeftT++;
      servoLeftT.write(posLeftT);
    }
    servoRightT.write(posRightT);
    delay(15);
  }
  digitalWrite(ledOpenT, HIGH);
  digitalWrite(ledClosedT, LOW);
}

//A timeout of +-3 seconds is granted and calls the closeDoor() function
//You can modify the wait time in the variable minimalSecondsT
void beforeCloseDoor() {
  unsigned long currentMillisDoorT = millis();
  if ((unsigned long)(currentMillisDoorT - timeElapsedDoorClosingT) >= waitingDoorClosingT ) {
    continuousSecondsT++;
    if (continuousSecondsT == minimalSecondsT)
      closeDoor();
    timeElapsedDoorClosingT = millis();
  }
}

//This works in a manner contrary to the openDoor() function
void closeDoor() {
  flagT = false;
  posLeftT = 180;
  for (posRightT = 0; posRightT <= 90; posRightT += 1) {
    if (posLeftT >= 90) {
      posLeftT--;
      servoLeftT.write(posLeftT);
    }
    servoRightT.write(posRightT);
    delay(15);
  }
  continuousSecondsT = 0;
  digitalWrite(ledClosedT, HIGH);
  digitalWrite(ledOpenT, LOW);
}
