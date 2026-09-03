---
title: "FIGURAS"
description: "REPORTE DE PRACTICA DE FIGURAS"
pubDate: "Mar 24, 2024"
heroImage: "https://fondosmil.co/fondo/18826.jpg"
---

**NOMBRE** : JUAN ALBERTO ARVIZU CASTILLO <br>
**SEMESTRE** : 2do Semestre<br>
**CARRERA** : INGENIERIA EN SISTEMAS COMPUTACIONALES

<hr>

## Introduccion

En el presente informe se detalla la práctica realizada para implementar la clase abstracta Figura, siguiendo las instrucciones dadas en clase. Esta práctica se enmarca en el contexto de la programación orientada a objetos, con el objetivo de entender los conceptos de abstracción, herencia y polimorfismo, así como su aplicación en la resolución de problemas relacionados con el cálculo de áreas y perímetros de diversas figuras geométricas.

<hr>

### Indice

1. Teoría
2. Desarrollo <br>
2.1. Enunciado <br> 
2.2 Diagrama de Flujo <br> 
2.3 Diagrama de Clases <br> 
2.4 Código <br> 
2.5 Pruebas Funcionales
3. Conclusiones
4. Bibliografía

<hr>

## Teoria

#### Conceptos

##### Clases Abstractas:

Son aquellas que no pueden ser instanciadas directamente, sino que sirven como plantillas para otras clases. Pueden contener métodos abstractos, es decir, métodos que no tienen una implementación definida en la clase abstracta, dejando su implementación a las clases hijas.

##### Polimorfismo:

Permite que objetos de diferentes clases respondan al mismo mensaje, comportándose de manera distinta según su tipo.

##### Encapsulamiento:

Es el mecanismo que permite ocultar la implementación interna de un objeto y restringir el acceso a sus datos, solo permitiendo interactuar con ellos a través de métodos específicos.

##### @Override:

Indica que un método de una clase hija está sobrescribiendo (o implementando de forma específica) un método de la clase padre. Esto significa que el método en la clase hija tiene la misma firma (nombre y parámetros) que el método en la clase padre.

Al utilizar @Override, el compilador verifica que estés realmente sobrescribiendo un método existente en la superclase, ayudándote a evitar errores de sintaxis.

##### Ejemplo
```markdown
```java 
class Animal {
    void hacerSonido() {
        System.out.println("Sonido de animal genérico");
    }
}

class Perro extends Animal {
    @Override
    void hacerSonido() {
        System.out.println("Guau");
    }
}
```

##### extends:

Es un operador en Java que se utiliza para verificar si un objeto es una instancia de una clase, una subclase o una interfaz. Retorna true si el objeto es una instancia del tipo especificado o de una de sus subclases, y false en caso contrario.

##### instanceof:

Es una palabra clave en Java que se utiliza en la declaración de clases para indicar que una clase es una subclase de otra clase. En otras palabras, una clase que "extiende" otra clase hereda los campos y métodos de la clase extendida.

## Desarrollo

#### Enunciado

La problemática que se resuelve no representa una dificultad grave, sino más bien una oportunidad para desarrollar una herramienta de apoyo.

Se busca implementar una jerarquía de clases que representen diversas figuras geométricas. Estas figuras geométricas, que incluyen círculos, cuadrados, triángulos, pentágonos, entre otras, deben depender de una clase base llamada Figura.

Cada figura debe contar con métodos para calcular tanto su área como su perímetro.

Muchas de las veces los usuarios tienen la dificultad o requieren de la practicidad de tener un software o app o hasta un simple script que les facilite su tarea diaria o alguna tarea que sea muy repetitiva, en este caso esta practica de figuras

Al tu ingresar al script en este caso te muestra un menu con varias opciones (Circulo, Cuadrado, Triangulo, Pentagono) y al elegir uno de esos te pide los requisitos necesarios para realizar el calculo de el area de la figura seleccionada, retornando asi el area de la figura seleccionada.

#### Diagrama de Clases

![DiagramadeClases placeholder](https://github.com/ArZz04/Figuras/blob/master/resources/clases.png?raw=true)
Fuente: `Aportada por el profesor`

#### Diagrama de Flujo

![DiagramadeClases placeholder](https://s1.significados.com/foto/diagrama-de-flujo-tipo-vertical.jpg?class=article)
Fuente: `de los deseos`

### Pruebas Funcionales
##### Recurso 1.0
![Recurso placeholder](https://github.com/ArZz04/Figuras/blob/master/resources/PF1.png?raw=true)
##### Recurso 1.1
![Recurso placeholder](https://github.com/ArZz04/Figuras/blob/master/resources/PF2.png?raw=true)
##### Recurso 1.2
![Recurso placeholder](https://github.com/ArZz04/Figuras/blob/master/resources/PF3.png?raw=true)
##### Recurso 1.3
![Recurso placeholder](https://github.com/ArZz04/Figuras/blob/master/resources/PF4.png?raw=true)
### CODIGOS

<script src="https://gist.github.com/ArZz04/e37255f39f5326c53f28051cb3c7ae16.js"></script>

#### Conclusiones

En el desarrollo de esta práctica, logre medio entender y creo que tambien supe usar o aplicar los conceptos de clases abstractas, polimorfismo y encapsulamiento. `(Que esten bien usados es otra cosa JAJAJAJ)` La creacion del menu es la que facilitara la interacción con el usuario y permitira una mejor comprension y funcionamiento del programa o script.


#### Bibliografia

> Area de Figura Geometrica. <br>  <cite>[math3logic](https://math3logic.com/area-de-figura-geometricas)</cite>

> Documentacion de Java (Extends) <br> <cite>[developer.mozilla](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Classes/extends)</cite>

> Ejemplo de Override <br> <cite>[tutorialesprogramacionya](https://www.tutorialesprogramacionya.com/javaya/detalleconcepto.php?punto=96&codigo=176&inicio=80)</cite>
