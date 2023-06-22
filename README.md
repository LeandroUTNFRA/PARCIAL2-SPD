# ALUMNO
Leandro Escobar 1J

# PARCIAL2-SPD
sistema incendio

# ESQUEMA
![ESQUEMA](https://github.com/LeandroUTNFRA/PARCIAL2-SPD/assets/122940722/d4b455a8-8e0b-4321-b53d-00ee3644db65)

# CIRCUITO
![ARDUINO](https://github.com/LeandroUTNFRA/PARCIAL2-SPD/assets/122940722/af464119-e779-431c-af27-91069e64284a)

# DEFINICION DE PINES 
cpp
//BIBLIOTECAS
#include<LiquidCrystal.h>
#include <Servo.h>
#include <IRremote.h>


//DEFINICION DE PINES
#define D4 4
#define D5 5 
#define D6 6
#define D7 7
#define E 2
#define RS 3
#define IR 8
#define ROJO 13
#define VERDE 12
#define BOTON1 0xEF10BF00
#define BOTON2 0xEE11BF00
bool encendido = true;
  

//CLASE SERVO
Servo miServo;

// Inicializar el objeto LiquidCrystal
LiquidCrystal lcd(RS,E,D4,D5,D6,D7);

//FUNCIONES
void MoverServo();
void MostrarEstacion();
