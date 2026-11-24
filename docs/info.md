<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

El proyecto implementa un reloj digital de 24 horas con formato HH:MM:SS, desarrollado mediante lógica combinacional y secuencial en Wokwi para su implementación como ASIC con Tiny Tapeout y la tecnología GF180MCU. No requiere un microcontrolador para realizar el conteo ni la decodificación de los dígitos.

Una señal de reloj externa de 1 kHz alimenta todos los flip-flops D por flanco ascendente. Un contador síncrono de 10 bits, con módulo 1000, produce una habilitación de conteo cada segundo. Los segundos y minutos se representan mediante contadores BCD de unidades (0–9) y decenas (0–5). Las horas utilizan dos dígitos BCD con límite de 23; después de 23:59:59, el contador regresa a 00:00:00.

Un registro circular de seis flip-flops D activa una posición por ciclo de reloj. El multiplexor selecciona el valor BCD del dígito activo y lo entrega a un único decodificador de siete segmentos. Las ocho líneas A–G y DP se comparten entre los seis displays; otras seis líneas seleccionan individualmente sus cátodos comunes. La selección es activa en bajo y los segmentos son activos en alto. El tiempo nominal por posición es de 1 ms, con una frecuencia de refresco aproximada de 166.7 Hz por display. Los puntos decimales de las unidades de horas y de minutos separan los grupos de dígitos.

El diseño incorpora dos entradas de ajuste manual, MIN_N y HOUR_N, activas en bajo, con detección de flanco de pulsación. MIN_N incrementa los minutos, propagando el acarreo a horas cuando corresponde; HOUR_N incrementa las horas en módulo 24. RST_N realiza un reinicio síncrono activo en bajo e inicializa tanto los contadores como el registro de barrido.

## How to test

-Seleccionar el proyecto en la placa de desarrollo.
-Aplicar una señal de reloj de 1000 Hz al pin CLK. Mantener MIN y HOUR en nivel alto cuando no se utilicen.
-Llevar RST_N a nivel bajo durante varios flancos ascendentes y volver a nivel alto. El reloj debe mostrar 00:00:00.
-Verificar que los segundos incrementen una unidad por cada 1000 flancos ascendentes del reloj y que 00:00:59 cambie a 00:01:00.
-Aplicar un pulso activo en bajo, sin rebote y de duración suficiente para ser muestreado, a MIN (ui[0]) y a HOUR (ui[1]) por separado. Verificar el incremento de minutos y horas, respectivamente.
-Comprobar el cambio de 23:59:59 a 00:00:00. Para acelerar las pruebas lógicas en simulación se puede aumentar temporalmente la frecuencia de reloj dentro de los límites del simulador.
-Con un analizador lógico, comprobar que uio[0]–uio[5] seleccionan un solo dígito a la vez en régimen normal, y que uo[0]–uo[7] entregan el patrón de segmentos correspondiente.

## External hardware

6 × SC56-11EWA: Displays LED de siete segmentos, cátodo común, color rojo, de 0.56 pulgadas.

1 × SN74AHCT244: Buffer de ocho canales para controlar las señales de los segmentos A, B, C, D, E, F, G y DP.

1 × SN74AHCT14: Circuito integrado con seis inversores Schmitt Trigger para controlar la activación de los displays.

6 × AO3400A: Transistores MOSFET de canal N para la conmutación de los cátodos comunes.

1 × SN74LVC14A: Circuito integrado con seis inversores Schmitt Trigger para acondicionar las señales de los pulsadores.

3 × Pulsadores normalmente abiertos: Utilizados para RESET, ajuste de minutos (MIN) y ajuste de horas (HORA).

8 × Resistencias de 680 Ω: Limitación de corriente para los segmentos de los displays.

9 × Resistencias de 10 kΩ: Resistencias pull-up para las señales de control.

3 × Resistencias de 1 kΩ: Utilizadas en los circuitos antirrebote de los pulsadores.

6 × Resistencias de 100 Ω: Limitación de corriente transitoria en las compuertas de los MOSFET.

14 × Resistencias de 100 kΩ: Resistencias pull-down para mantener niveles lógicos definidos.

3 × Capacitores de 4.7 µF: Filtrado de las señales de los pulsadores.

3 × Capacitores cerámicos de 100 nF: Desacoplo de alimentación de los circuitos integrados externos.

2 × Capacitores de 10 µF: Filtrado y estabilización de la alimentación.

1 × Generador de reloj de 1000 Hz: Señal cuadrada de 0 a 3.3 V. Puede utilizarse el generador integrado de la Tiny Tapeout Demo Board.

1 × Fuente regulada de 5 V: Alimentación para los controladores de los displays, con capacidad de 500 mA o superior.
