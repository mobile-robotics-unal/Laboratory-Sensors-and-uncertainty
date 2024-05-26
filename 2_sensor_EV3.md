# Condiciones iniciales y montaje.

# Desplazamiento lineal (100 cm) - Velocidad angular (30% y 45%).
Empleando el software MINDSTORMS EV3 y de acuerdo a las condiciones iniciales del montaje, y dimensiones de la rueda, se calcula el número de grados que debe girar cada rueda para alcanzar los $100\,cm$. Para ello se tiene en cuenta un radio de la rueda de $2.78\,cm$. El conjunto de operaciones a realizar en el bloque se muestran a continuación:

$$
\left.deg(x)\right|_{x=100cm}=\frac{100cm \cdot 360^\circ}{2\pi\cdot2.78cm}\approx 2061^\circ
$$

Este será el número de grados que será tenido en cuenta para posprocesamiento de los datos. Así mismo, el siguiente de conjunto de bloques de programación de desplazamiento lineal, lectura del sensor de ultrasonido y cálculo anterior pero con una aproximación de $2\pi\approx6.28$.

![desp_30_100](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/ad97f637-43a5-456d-8157-a92d0df01004)

A continuación se muestra el panel de lectura de los datos de cada motor así como la distancia que detecta el sensor de ultrasonido:

![desp_30_100_1](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/8de180a8-7257-4d33-8bc8-59b369e0646b)

## Toma de datos.
Posterior al montaje experimental, se toman los siguientes datos en la posición inicial y final:
* Distancia inicial y final del EV3 (cinta métrica).
* Lecturas de distancia obtenidas por el sensor de ultrasonido.
* Lectura de desplazamiento angular de los enconder de cada motor.
  
Los valores de incertidumbre de cada elemento son:
* Cinta métrica: $\pm1mm$
* Sensor de ultrasonido: $\pm1mm$
* Encoder: $\pm 1^\circ$

### Velocidad angular al 30%.
*Tabla 1*: Datos de distancia recorrida experimentalmente, para una distancia ideal de $100cm$ a un $30\%$ de la velocidad nominal de los motores.

![desp_30_100_tabla](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/cb564b9b-1d89-48b1-8c34-3da174972966)

Posteriormente con estos datos se realizo un post-procesamiento en MATLAB para la obtención de gráficas de *Error relativo* y *Error absoluto*, así como los valores de la media $\mu$ y desviación estándar $\sigma$. El código comentado para este procesamiento, el cual se muestra a continuación:

``` matlab
clear all
clc
%% Lectura de los datos experimentales
%Conjunto de muestras
n = [1,2,3,4,5];
%Valor ideal
desp_ideal = 100;
desp_ang_ideal     = ((desp_ideal*360)/(2*pi*2.78));
desp_ideal_vec     = desp_ideal*ones(1,5);
desp_ang_ideal_vec = desp_ang_ideal*ones(1,5);
% Lectura cinta.
cinta_final = [99.0,99.2,99.3,99.5,99.2];
% Lectura ultrasonido
ultra_ini = [116.4,118.7,120.6,118.9,116.6];
ultra_fin = [16.6,17.3,17.5,18.7,16.6];
% Lectura encoders
enc_b_ini = [4,0,2,-4,-1];
enc_b_fin = [2066,2066,2067,2066,2067];
enc_c_ini = [0,1,0,1,8];
enc_c_fin = [2068,2068,2068,2067,2068];

%% Procesamiento de los datos.
% Delta de desplazamiento lineal y angular.
delta_ultra = ultra_ini - ultra_fin;
delta_enc_b = enc_b_fin - enc_b_ini;
delta_enc_c = enc_c_fin - enc_c_ini;
% Error absoluto de cada conjunto de datos.
abs_er_cinta = abs(cinta_final - desp_ideal_vec);
abs_er_ultrasound = abs(delta_ultra - desp_ideal_vec);
abs_er_encoder_b  = abs(delta_enc_b - desp_ang_ideal_vec);
abs_er_encoder_c  = abs(delta_enc_c - desp_ang_ideal_vec);
% Error relativo de cada conjunto de datos.
relativo_er_cinta = (abs(cinta_final - desp_ideal_vec)/desp_ideal)*100;
relativo_er_ultrasound = (abs(delta_ultra - desp_ideal_vec)/desp_ideal)*100;
relativo_er_encoder_b  = (abs(delta_enc_b - desp_ang_ideal_vec)/desp_ang_ideal)*100;
relativo_er_encoder_c  = (abs(delta_enc_c - desp_ang_ideal_vec)/desp_ang_ideal)*100;

% Graficas de error absoluto.
figure()
subplot(2,2,1)
scatter(n,abs_er_cinta,'r')
xlabel('Muestra [n]')
ylabel('E_a [cm]')
grid on
title('Error absoluto - cinta métrica')

subplot(2,2,2)
scatter(n,abs_er_ultrasound,'r')
xlabel('Muestra [n]')
ylabel('E_a [cm]')
grid on
title('Error absoluto - Ultrasonido')

subplot(2,2,3)
scatter(n,abs_er_encoder_b,'r')
xlabel('Muestra [n]')
ylabel('E_a [deg]')
grid on
title('Error absoluto - Encoder B')

subplot(2,2,4)
scatter(n,abs_er_encoder_c,'r')
xlabel('Muestra [n]')
ylabel('E_a [deg]')
grid on
title('Error absoluto - Encoder C')

% Graficas de error relativo.
figure()
subplot(2,2,1)
scatter(n,relativo_er_cinta,'b')
xlabel('Muestra [n]')
ylabel('E_r [%]')
grid on
title('Error relativo - cinta métrica')

subplot(2,2,2)
scatter(n,relativo_er_ultrasound,'b')
xlabel('Muestra [n]')
ylabel('E_r [%]')
grid on
title('Error relativo - Ultrasonido')

subplot(2,2,3)
scatter(n,relativo_er_encoder_b,'b')
xlabel('Muestra [n]')
ylabel('E_r [%]')
grid on
title('Error absoluto - Encoder B')

subplot(2,2,4)
scatter(n,relativo_er_encoder_c,'b')
xlabel('Muestra [n]')
ylabel('E_r [%]')
grid on
title('Error absoluto - Encoder C')

%Cálculo de la media y desviación estándar.

mu_cinta = mean(cinta_final);
mu_ultra = mean(delta_ultra);
mu_enc_b = mean(delta_enc_b);
mu_enc_c = mean(delta_enc_c);

sigma_cinta = std(cinta_final);
sigma_ultra = std(delta_ultra);
sigma_enc_b = std(delta_enc_b);
sigma_enc_c = std(delta_enc_c);
```
El *Error absoluto* se presenta a continuación, en el cual se observa que...

![desp_30_abs_er](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/d38bc416-271c-4154-9088-60ef222801ae)

De manera similar el *Error relativo* sirve como un mejor indicativo, puesto que...
![desp_30_abs_rel](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/69ae5232-5545-44a9-936e-cb4d292a5b96)

### Velocidad angular al 100%

*Tabla 2*: Datos de distancia recorrida experimentalmente, para una distancia ideal de $100cm$ a un $100\%$ de la velocidad nominal de los motores.

# Giro ruedas en intervalos de 30° y 45°.

## Análisis de datos.

