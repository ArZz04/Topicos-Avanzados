---

title: 'RAÍCES DE UNA ECUACIÓN CUADRÁTICA'

description: 'REPORTE DE PRACTICA DE ECUACIONES CUADRÁTICAS'

pubDate: 'September 6, 2026'

heroImage: '/rec11/PORTDA.png'

---

**NOMBRE** : JUAN ALBERTO ARVIZU CASTILLO <br>

**SEMESTRE** : 5to Semestre<br>

**CARRERA** : INGENIERÍA EN SISTEMAS COMPUTACIONALES

<hr>

## Introducción

En esta práctica se desarrolló un programa en C# encargado de resolver ecuaciones cuadráticas de la forma ax² + bx + c = 0 utilizando la fórmula general.

El sistema evalúa el discriminante (Δ = b² - 4ac) para clasificar el tipo de soluciones obtenidas (dos raíces reales distintas, una raíz real doble o raíces imaginarias/complejas) y muestra los resultados formateados en pantalla.

Además, el programa incluye mecanismos de validación para garantizar que el coeficiente 'a' sea distinto de cero y evitar errores numéricos durante el cálculo de divisiones o raíces cuadradas de números negativos.

<hr>

### Índice

2.3. Código <br>

2.4. Pruebas Funcionales

3. Conclusiones

<hr>


#### Enunciado

Realizar un programa que determine las raíces de una ecuación cuadrática de la forma ax² + bx + c = 0.

El programa deberá solicitar al usuario los valores de ''a'', ''b'' y ''c'' y utilizar la fórmula general:

x = (-b ± √(b² - 4ac)) / 2a

El programa deberá determinar y mostrar:
- Si la ecuación tiene dos raíces reales diferentes.
- Si tiene una raíz real doble.
- Si las raíces son irreales (complejas).

### Pruebas Funcionales

#### Recurso 1.0 - Dos raíces reales diferentes

Prueba ejecutada con coeficientes donde el discriminante es positivo (a = 1, b = -5, c = 6).

![Recurso placeholder](/rec11/RDEF.png)

#### Recurso 1.1 - Una raíz real doble

Prueba ejecutada con coeficientes donde el discriminante es cero (a = 1, b = -4, c = 4).

![Recurso placeholder](/rec11/EQUAL.png)

#### Recurso 1.2 - Raíces complejas (irreales)

Prueba ejecutada con coeficientes donde el discriminante es negativo (a = 1, b = 2, c = 5).

![Recurso placeholder](/rec11/IRREAL.png)

### CÓDIGOS

<script src="https://gist.github.com/ArZz04/77986875775991a9e33962a32b30a01a.js"></script>

#### Conclusiones

En el desarrollo de esta práctica se aplicaron estructuras condicionales compuestas (if, else if, else) para bifurcar el flujo del programa según el valor del discriminante.

Se hizo uso de métodos de la clase Math de C#, como Math.Sqrt y Math.Abs, para realizar operaciones matemáticas complejas de forma rápida y eficiente.

Finalmente, se reforzó la importancia del manejo de tipos numéricos double para mantener la precisión decimal y el tratamiento explícito de números complejos cuando el discriminante resulta ser menor a cero.

