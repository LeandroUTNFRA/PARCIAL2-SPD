# ALUMNO
Leandro Escobar 1J

# PARCIAL2-SPD

sistema incendio

# ESQUEMA

![ESQUEMA](https://github.com/LeandroUTNFRA/PARCIAL2-SPD/assets/122940722/d4b455a8-8e0b-4321-b53d-00ee3644db65)

# CIRCUITO
![ARDUINO](https://github.com/LeandroUTNFRA/PARCIAL2-SPD/assets/122940722/af464119-e779-431c-af27-91069e64284a)

# COMPONENTES
![COMPONENTES](https://github.com/LeandroUTNFRA/PARCIAL2-SPD/assets/122940722/e8355a85-7611-44f7-9463-63e74916c9fa)


# Conexión de una pantalla LCD 16x2
Este documento describe cómo conectar una pantalla LCD de 16x2 caracteres a un Arduino. A continuación se muestra un esquema de conexión y una descripción de los pines utilizados.

- Materiales necesarios
- Arduino Uno
- Pantalla LCD 16x2
- Potenciómetro de 10k ohmios
- Cables de conexión
- 
# Esquema de conexión
Conecta los componentes según el siguiente esquema:

      LCD       |     Arduino Uno
------------------------------------
       VSS      |        GND
       VDD      |        5V
       VO       |    Potenciómetro central
       RS       |        Pin digital 3
       RW       |        GND
       E        |        Pin digital 2
       D4       |        Pin digital 4
       D5       |        Pin digital 5
       D6       |        Pin digital 6
       D7       |        Pin digital 7
       A        |        5V
       K        |        GND

# Descripción de los pines
- VSS, VDD: Alimentación de la pantalla LCD (GND y 5V, respectivamente).
- VO: Control de contraste de la pantalla LCD. Conéctalo al potenciómetro de 10k ohmios, y conecta los extremos del potenciómetro a 5V y GND, respectivamente.
- RS: Selecciona la instrucción de registro o datos. Conéctalo al pin digital 12 del Arduino.
- RW: Lectura/escritura de la pantalla LCD. Conéctalo a GND para establecer el modo de escritura.
- E: Habilita la lectura/escritura de datos. Conéctalo al pin digital 11 del Arduino.
- D4-D7: Conexiones de datos de 4 bits de la pantalla LCD. Conéctalos a los pines digitales 5, 4, 3 y 2 del Arduino, respectivamente.
- A, K: Retroiluminación de la pantalla LCD (5V y GND, respectivamente).

Recuerda que es importante utilizar una biblioteca como LiquidCrystal para facilitar la comunicación con la pantalla LCD y realizar las operaciones necesarias para mostrar texto o caracteres en ella.

# Conexión de un Control IR
Este documento describe cómo conectar un control infrarrojo a un Arduino. A continuación se muestra un esquema de conexión y una descripción de los pines utilizados.

- Materiales necesarios
- Arduino Uno
- Control IR
- Receptor IR
- Cables de conexión
- Esquema de conexión
- 
# Conecta los componentes según el siguiente esquema:
     Control IR    |    Arduino Uno
------------------------------------
       VCC        |       5V
       GND        |       GND
       OUT        |    Pin digital 11
       
Conecta el pin VCC del control IR al pin 5V del Arduino para proporcionarle alimentación. Conecta el pin GND del control IR al pin GND del Arduino para establecer la referencia de tierra. Finalmente, conecta el pin OUT del control IR al pin digital 11 del Arduino.

# Descripción de los pines
- VCC: Alimentación del control IR. Conéctalo al pin 5V del Arduino.
- GND: Referencia de tierra del control IR. Conéctalo al pin GND del Arduino.
- OUT: Salida de señal del control IR. Conéctalo al pin digital 11 del Arduino.

# DEFINICION DE PINES 
~~~cpp
//BIBLIOTECAS
#include<LiquidCrystal.h>
#include <Servo.h>
#include <IRremote.h>


#define IR 8
#define ROJO 13
#define VERDE 12
#define BOTON1 0xEF10BF00
#define BOTON2 0xEE11BF00
bool encendido = true;
  
//CLASE SERVO
Servo miServo;

// Inicializar el objeto LiquidCrystal
LiquidCrystal lcd(3,2,4,5,6,7);

//FUNCIONES
void MoverServo();
void MostrarEstacion();
void MostrarMensaje(char mensaje,int temperatura);
~~~

# LOOP
La función loop() es parte del ciclo de ejecución principal de un programa en Arduino. En este caso, la función se encarga de realizar las siguientes acciones repetidamente:

- Verifica si se ha recibido una señal infrarroja utilizando el receptor IR. Si se ha recibido una señal, se procede a realizar las acciones correspondientes según el código decodificado.
- Si el código decodificado corresponde al valor de BOTON1, se establece la variable encendido como false y se muestra el mensaje "encendido" a través del puerto serial.
- Si el código decodificado corresponde al valor de BOTON2, se establece la variable encendido como true y se muestra el mensaje "apagado" a través del puerto serial.
- Se utiliza el método resume() del objeto IrReceiver para habilitar la recepción del próximo valor del receptor IR.
- Si encendido es igual a false, se llama a la función MostrarEstacion() para mostrar la estación del año y la temperatura en la pantalla LCD.
- Si encendido es igual a true, se limpia la pantalla LCD, y se apagan los LEDs indicadores (ROJO y VERDE) configurados en los pines correspondientes.
~~~cpp
void loop()
{
 if (IrReceiver.decode()) {
    if(IrReceiver.decodedIRData.decodedRawData == BOTON1){
    	encendido=false;
        Serial.println("encendido");
    }
   else if (IrReceiver.decodedIRData.decodedRawData == BOTON2){
   		encendido=true;
		Serial.println("apagado");
   
   }
      IrReceiver.resume(); // Enable receiving of the next value
    	
  }
  if(encendido == false){
  	MostrarEstacion();
  }
  else{
  	lcd.clear();
    digitalWrite(ROJO,LOW);
    digitalWrite(VERDE,LOW);
  }
}
~~~

# FUNCIONES PRINCIPALES:
# Funcion MostrarEstacion()

Esta función se encarga de mostrar la estación del año y la temperatura actual en una pantalla, basándose en la lectura de un sensor de temperatura. También controla el encendido de luces indicadoras según la estación y una condición de temperatura extrema.

Descripción
La función MostrarEstacion() realiza los siguientes pasos:

Lee el valor del sensor de temperatura conectado al pin analógico A0 y lo guarda en la variable sensor.
Utiliza la función map() para convertir el valor del sensor en una temperatura en grados Celsius, y guarda el resultado en la variable temperatura.
Comprueba el rango de temperatura para determinar la estación del año y dependiendo la temperatura va cambiar el mensaje de la estacion del año

~~~cpp
void MostrarEstacion(){
  int sensor = analogRead(A0);
  int temperatura = map(sensor,20,358,-40,125);
  if (temperatura >= -5 && temperatura <=10){
  	MostrarMensaje("invierno",temperatura);
  	digitalWrite(ROJO,LOW);
    digitalWrite(VERDE,HIGH);
  }
  else if (temperatura>=11 && temperatura<=16){
  	MostrarMensaje("otonio",temperatura);
    digitalWrite(ROJO,LOW);
    digitalWrite(VERDE,HIGH);
  
  }
  else	if (temperatura>=17 && temperatura<=24)
  {
    MostrarMensaje("primavera",temperatura);
    digitalWrite(ROJO,LOW);
    digitalWrite(VERDE,HIGH);
  }
  else	if (temperatura>=25 && temperatura<=40)
  {
    MostrarMensaje("verano",temperatura);
    digitalWrite(ROJO,LOW);
    digitalWrite(VERDE,HIGH);
  }
  else if (temperatura>=60){
  	MostrarMensaje("INCENDIO",temperatura);
    digitalWrite(ROJO,HIGH);
    digitalWrite(VERDE,LOW);
  	MoverServo();
  }
}
~~~
# MoverServo()
Esta función se encarga de controlar un servo motor para realizar un movimiento específico.

Descripción
La función MoverServo() realiza los siguientes pasos:

- Utiliza el objeto miServo para llamar al método write() y mover el servo motor a una posición de 0 grados.
- Espera un tiempo de 500 milisegundos utilizando la función delay() para dar tiempo al servo motor de alcanzar la posición deseada.
- Utiliza nuevamente el objeto miServo y el método write() para mover el servo motor a una posición de 180 grados.
- La función termina su ejecución.

~~~cpp
void MoverServo(){
	miServo.write(0);
  	delay(500);
  	miServo.write(180);
}
~~~

# MostrarMensaje(const char* mensaje, int temperatura)
Esta función se encarga de mostrar un mensaje y una temperatura en una pantalla LCD.

# Parámetros
mensaje: Un puntero a una cadena de caracteres (const char*) que contiene el mensaje a mostrar en la pantalla.
temperatura: Un entero que representa la temperatura a mostrar en la pantalla.

Descripción
La función MostrarMensaje() realiza los siguientes pasos:

- Utiliza el objeto lcd para llamar al método print() y mostrar el mensaje en la pantalla LCD.
- Utiliza el método setCursor() para establecer la posición del cursor en la columna 0, fila 1 de la pantalla LCD.
- Utiliza nuevamente el objeto lcd y el método print() para mostrar la temperatura concatenada con el símbolo "C°" en la segunda línea de la pantalla.
- Espera un tiempo de 200 milisegundos utilizando la función delay() para dar tiempo al usuario para leer la información mostrada en la pantalla.
- Utiliza el método clear() del objeto lcd para borrar el contenido de la pantalla LCD.

~~~cpp
void MostrarMensaje(const char* mensaje,int temperatura){

  lcd.print(mensaje);
    lcd.setCursor(0, 1);
    lcd.print(String(temperatura) + "C°");
    delay(200);
    lcd.clear();
}
~~~
# LINK DE PROYECTO
https://www.tinkercad.com/things/9ywV6ID3Bsl-2parcial-spd-leandro-escobar/editel?sharecode=3Z4q6UUuJvBG7wV9H22DIgbEl7jiVfg-DuYBENhcsC0

# Fin del Proyecto
gracias por ver 


- ![MuaKissGIF](https://github.com/LeandroUTNFRA/PARCIAL2-SPD/assets/122940722/66fcef9f-f795-4ec2-99e0-afb9aa70c613)
