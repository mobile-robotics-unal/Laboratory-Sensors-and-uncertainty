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

# Giro ruedas en intervalos de 30° y 45°.


