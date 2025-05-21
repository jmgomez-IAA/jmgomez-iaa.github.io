---
title: "Uso de servomotores con Arduino - Servo SG90"
date: 2025-05-21
categories: [Arduino, Tutoriales]
tags:
  - arduino
  - servo
  - info
---

## Introducción

En este tutorial se explicará el funcionamiento y características de un servomotor, Servo SG90, así como la conexión con la placa Arduino y su utilización.

## Librería servo de Arduino

El control de servos en Arduino es muy fácil porque hay disponible una librería completa para el control de los servomotores. La documentación completa se encuentra en su página oficial: [Servo Library](https://www.arduino.cc/en/Reference/Servo).

### Funciones principales

- `attach(pin, min, max)`: Vincula la variable Servo a un pin.
- `write(grados)`: Hace girar el eje del servo un número de grados según se le indique (0º a 180º).
- `writeMicroseconds(tiempo)`: Indica la anchura de pulso (en microsegundos) al servo.
- `read()`: Lee la posición actual del servo y devuelve un valor entre 0 y 180.
- `attached()`: Verifica si la variable servo está unida al pin indicado.
- `detach()`: Desvincula la variable servo del pin indicado.

## Esquema para su montaje

<img src="/assets/2025-05-21-Uso-de-servomotores-con-arduino-servo-sg90/arduino-sg90-sch.png">

El servo dispone de 3 cables: dos para su alimentación (GND y VCC) y uno de señal (Sig). 

<img src="/assets/2025-05-21-Uso-de-servomotores-con-arduino-servo-sg90/sg90-pinout.png">

Es recomendable utilizar una fuente de alimentación externa, ya que Arduino puede proporcionar corriente suficiente para un servo pequeño como el SG90, pero no dispone de la suficiente energía para actuar con un servo grande o varios servos pequeños.

**Material necesario:**

- Placa Arduino UNO o similar.
- Servo SG90.
- Cables.

Para el control del servo, conectamos el cable amarillo (señal) al pin 2.

## Código para girar el motor de 0º a 180º

```cpp
#include "Servo.h"   // Librería para poder controlar el servo

Servo servoMotor;   // Crea un objeto servo llamado servoMotor

void setup(){ 
  // Asociamos el servo a la patilla 2 del Arduino
  servoMotor.attach(2);
}

void loop(){
  // Desplazamos a la posición 0º
  servoMotor.write(0);
  delay(1000);
  
  // Desplazamos a la posición 180º
  servoMotor.write(180);
  delay(1000);
}
```

## Girando grado a grado nuestro servomotor

```cpp
#include "Servo.h"

Servo servoMotor;

void setup(){
  servoMotor.attach(2);
}

void loop(){
  for(int pos = 0; pos <= 180; pos++){
    servoMotor.write(pos);
    delay(15);
  }
  for(int pos = 180; pos >= 0; pos--){
    servoMotor.write(pos);
    delay(15);
  }
}

```


## Control de un servomotor a través de un potenciómetro

<img src="/assets/2025-05-21-Uso-de-servomotores-con-arduino-servo-sg90/arduino-sg90-pot-sch.png">

```cpp
#include "Servo.h"

Servo servoMotor;
int potPin = A0; // Pin analógico donde conectamos el potenciómetro

void setup(){
  servoMotor.attach(2);
}

void loop(){
  int val = analogRead(potPin);           // Lee el valor del potenciómetro (0-1023)
  val = map(val, 0, 1023, 0, 180);        // Mapea el valor a un rango de 0 a 180
  servoMotor.write(val);                  // Mueve el servo a la posición correspondiente
  delay(15);
}

```


# Conclusiones

Sistema de archivos BTRFS verificado.

# Referencias
[Control de Servos SG90 con arduino](https://eloctavobit.com/proyectos-tutoriales-arduino/uso-de-servomotores-con-arduino-servo-sg90)
