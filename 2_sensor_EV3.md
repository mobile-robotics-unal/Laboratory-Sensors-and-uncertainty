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

## Toma de datos y análisis.
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

![desp_100_100_tabla](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/7819c092-1f79-451e-bd93-8566656bb0dd)

![desp_100_abs_er](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/6cea8dd3-0e93-4860-9cec-5288eb650973)

![desp_100_abs_rel](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/77264f55-71fe-45fc-8845-9df817332ea8)


# Giro ruedas en intervalos de 30° y 45°.
## Toma de datos y análisis.

Para la toma de datos se realizaron mediciones tomando intervalos de 30° y 45°. Posteriormente, usando el software Tracker, se midieron los ángulos de la posición de las ruedas sobre las imagenes tomadas.

### Intervalos de 30°

![giro30_0](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/45ee66d8-251c-46ce-9fd8-f516189cfd0a)

![giro30_30](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/7d33da63-36c7-4d78-a762-1beaa63ce3d6)

![giro30_60](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/6e76dbc8-9f18-459d-a474-bfadd9a96e94)

![giro30_90](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/e315c5cd-ebcc-47f5-93e2-71b0a03a09a7)

![giro30_120](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/132e90a9-3142-456a-b7cf-dc068634e15b)

![giro30_150](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/c2517e68-7f91-4f67-af95-f45cc03a57e3)

![giro30_180](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/3777c9ef-8b68-4b6e-b320-6dcb0d86ca99)

![giro30_210](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/ff01a6ba-fcc4-4a09-af8f-51c47c2fbb81)

![giro30_240](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/5010383c-22d4-4854-a5b0-153b5a560c83)

![giro30_270](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/66bbfc99-3b7e-466f-a388-18f60b0b9497)

![giro30_300](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/9c319fde-5df4-4463-a212-0a2cea787aa1)

![giro30_330](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/7cdd3c27-226b-4631-9cc2-ae072c4bfa25)

![giro30_360](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/92d2fe36-cba6-4603-ab16-381f4eb43d8c)


#### Tabla de resultados:
![30_table](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/blob/main/figures/encoders/30_table.png)
#### Gráfico de error absoluto:
![30_graph](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/blob/main/figures/encoders/30_graph.png)

### Intervalos de 45°


![giro45_0](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/341892fc-ced5-450d-a36a-9222cc001802)

![giro45_45](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/723db4f5-abf5-4091-ac54-9e5cf088ec51)

![giro45_90](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/4a2452dc-aae7-4b63-bc69-7b37c5d0649c)

![giro45_135](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/e8c7fd8c-d3bf-4bee-ad5d-e6f66e1ca2a2)

![giro45_180](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/e3b6a8df-f182-4a8e-8d3f-67b900015cae)

![giro45_225](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/b41674c5-05f8-45cc-b388-b5e91fb5b246)

![giro45_270](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/251dfa62-f8b0-4c77-b702-643f8ba12e44)

![giro45_315](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/a0f32dbb-0256-4090-8cff-c2977affb60b)

![giro45_360](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/assets/161974694/9503cc08-6fba-4e9a-a1e2-6886cd673eea)


#### Tabla de resultados:
![45_table](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/blob/main/figures/encoders/45_table.png)
#### Gráfico de error absoluto:
![45_graph](https://github.com/mobile-robotics-unal/Laboratory-Sensors-and-uncertainty/blob/main/figures/encoders/45_graph.png)

### Análisis

* Se observa que el error incremente a medida que se acerca a los 180°, posteriormente decae al volver a la posición inicial. 
* Los encoders del EV3 han llegado a tener errores de 9.1°.
* El error de los encoders aumenta considerablemente al hacer rotaciones mas largas, con 30° se tuvo un promedio de 3.075° de error, mientras que con 45° el error promedio fue de 6.25°, siendo más del doble de error con una diferencia de 15° por intervalo.
