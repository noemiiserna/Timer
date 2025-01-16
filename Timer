#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

int seconds = 0;
int minutes = 0;
int targetMinutes = 1;
bool isRunning = false;

unsigned long lastSecondUpdate = 0;
unsigned long lastBlinkUpdate = 0;
const int blinkInterval = 200; // 200 ms para el parpadeo
const int secondInterval = 1000; // 1 segundo
bool blinkState = false;

const int buttonStart = 2;
const int buttonIncrease = 3;
const int ledBlink = 8;
const int ledFinish = 9;
const int ledRunning = 11;
const int buzzer = 10;

bool startButtonState = false; // Estado actual del botón de inicio/parada
bool lastStartButtonState = true; // Último estado del botón para detectar cambios

void setup() {
  pinMode(buttonStart, INPUT_PULLUP);
  pinMode(buttonIncrease, INPUT_PULLUP);
  pinMode(ledBlink, OUTPUT);
  pinMode(ledFinish, OUTPUT);
  pinMode(ledRunning, OUTPUT);
  pinMode(buzzer, OUTPUT);

  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print(" Chronometer ");
  displayTime();
}

void loop() {
  handleButtons();

  if (isRunning) {
    updateChronometer();
    blinkLed();
    checkTargetTime();
  }
}

void handleButtons() {
  // Leer el botón de inicio/parada
  bool currentStartButtonState = digitalRead(buttonStart);
  if (!currentStartButtonState && lastStartButtonState) { // Detectar cambio de estado (toggle)
    isRunning = !isRunning; // Cambiar el estado de funcionamiento
    if (isRunning) {
      digitalWrite(ledFinish, LOW);
      digitalWrite(ledRunning, HIGH);
    } else {
      digitalWrite(ledRunning, LOW);
      digitalWrite(ledBlink, LOW);
    }
  }
  lastStartButtonState = currentStartButtonState;

  // Leer el botón para aumentar el tiempo objetivo
  if (digitalRead(buttonIncrease) == LOW) {
    delay(250); // Debounce
    targetMinutes++;
    if (targetMinutes >= 60) targetMinutes = 0;
    displayTargetMinutes();
  }
}

void updateChronometer() {
  if (millis() - lastSecondUpdate >= secondInterval) {
    lastSecondUpdate = millis();
    seconds++;
    if (seconds == 60) {
      seconds = 0;
      minutes++;
    }
    if (minutes == 60) {
      minutes = 0;
    }
    displayTime();
  }
}

void blinkLed() {
  if (millis() - lastBlinkUpdate >= blinkInterval) {
    lastBlinkUpdate = millis();
    blinkState = !blinkState;
    digitalWrite(ledBlink, blinkState);
  }
}

void checkTargetTime() {
  if (minutes == targetMinutes) {
    isRunning = false;
    resetChronometer();
    digitalWrite(ledFinish, HIGH);
    digitalWrite(ledRunning, LOW);
    digitalWrite(ledBlink, LOW);
    playBuzzer();
  }
}

void resetChronometer() {
  minutes = 0;
  seconds = 0;
  displayTargetMinutes();
}

void displayTime() {
  lcd.setCursor(8, 1);
  lcd.print(formatTime(minutes) + ":" + formatTime(seconds));
}

void displayTargetMinutes() {
  lcd.setCursor(0, 1);
  lcd.print("    ");
  lcd.setCursor(0, 1);
  lcd.print(targetMinutes);
  lcd.setCursor(3, 1);
  lcd.print("min");
}

String formatTime(int value) {
  return value < 10 ? "0" + String(value) : String(value);
}

void playBuzzer() {
  tone(buzzer, 2000); // Emite un tono de 2000 Hz
  delay(5000); // Mantiene el tono durante 5 segundos
  noTone(buzzer); // Detiene el tono
}
