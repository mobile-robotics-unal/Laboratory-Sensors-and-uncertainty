After *verify* and *upload* the code into the *Arduino UNO*. It's necessary to be sure that the serial port in the *Device Manager* (Windows) correspond to the serial port associated to the Arduino Board. Then, we open *Arduino IDE* and *usound3.ino* file and verify that the pins associated in the code to the trigger and echo are the same as the *Arduino Board*. In the next picture, the reader could see the pin connection in the *Arduino UNO* board.

<p align="center">
  <img align="center" height="200" src="https://github.com/mobile-robotics-unal/Sensors-and-uncertainty/assets/161974694/8d69556e-cf93-4ae9-81aa-564f9e70dbac">
<p/>

Using the next code developed by Dr.Ing. Ricardo Ramirez and PhD. Ing. Cárdenas, we verify and upload the code to the board. As a verification of performance of the code, we open the *serial monitor* and we could see the distance values in centimeters.
```c++
// declaración de variables para pines
const int pinecho = 11;
const int pintrigger = 12;
 
// variables para calculos
unsigned long tiempo;
float distancia;
 
/**
   Función setup: se ejecuta una vez cuando encendemos el arduino
*/
void setup()
{
  // preparar la comunicación serial
  Serial.begin(9600);
 
  // configurar los pines utilizados por el sensor
  pinMode(pinecho, INPUT);
  pinMode(pintrigger, OUTPUT);
 
 }
 
/**
   Función loop: se ejecuta continuamente mientras el arduino permanece encendido
*/
void loop()
{
  // asegurarnos que el pin trigger se encuentra en estado bajo
  digitalWrite(pintrigger, LOW);
  delayMicroseconds(2);
 
  // comenzamos pulso alto, debe durar 10 uS
  // luego regresamos a estado bajo
  digitalWrite(pintrigger, HIGH);
  delayMicroseconds(10);
  digitalWrite(pintrigger, LOW);
 
  // medimos el tiempo en estado alto del pin "echo"
  // el tiempo en estado alto es proporcional a la distancia medida
  tiempo = pulseIn(pinecho, HIGH);
 
  // LA VELOCIDAD DEL SONIDO ES DE 340 M/S O 29,4 MICROSEGUNDOS POR CENTIMETRO
  // DIVIDIMOS EL TIEMPO DEL PULSO ENTRE 58, TIEMPO QUE TARDA RECORRER IDA Y
  // VUELTA UN CENTIMETRO LA ONDA SONORA
  distancia = float(tiempo) / 58.8;
 
  // imprimir la distancia medida al monitor serial
  //Serial.print(F("Distancia: "));
 Serial.print(distancia);
 Serial.print('\n');
 // Serial.println(F(" cm"));
 
  // esperar 0,25 segundos antes de realizar otra medición
  delay(250);
}
```
Then, to obtain the experimental data for each distance between *1 to 2.5 m*, we run the *ultrasound3.m* file in *MATLAB R2024a*.
```matlab
% ULTRASOUND3 programa para capturar datos por el puerto serial. Previo a
% ejecutar el programa verifique mediante el Administrador de dispositivos
% el puerto alque está conectado el ARDUINO y modifique el número de puerto 
% en la instrucción PORT. Ricardo % Ramírez Heredia, Universidad Nacional 
% de Colombia, 2023. 

clear all;
port=serialport("COM7",9600,"DataBits",8);
flush(port)
nm=100; %Número de muestras.
figure(1)
clf
xlabel('Muestra')
ylabel('Distancia (cm)')
title('U_{sound} Data')
grid on;
hold on;
t=1:nm;
dist=zeros(1,nm);
for i=1:nm
      dist(i)=readline(port); 
      pause(.25)
end
plot(t,dist)
delete(port);
clear port
```
