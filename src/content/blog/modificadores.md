---
title: "MODIFICADORES DE ACCESO"
description: "INFORME SOBRE LOS DIFERENTES TIPOS DE MODIFICADORES DE ACCESO EN JAVA"
pubDate: "May 26, 2024"
heroImage: "https://tecnitium.com/wp-content/uploads/2023/06/encapsulamiento.png"
---

**NOMBRE** : JUAN ALBERTO ARVIZU CASTILLO <br>
**SEMESTRE** : 2do Semestre<br>
**CARRERA** : INGENIERIA EN SISTEMAS COMPUTACIONALES

<hr>

## Introduccion

Los modificadores de acceso en Java son palabras clave que se utilizan para controlar el acceso a clases, variables, métodos y constructores en un programa. Estos modificadores determinan si una clase, variable, método o constructor es accesible desde otras clases o paquetes. En Java, hay cuatro tipos principales de modificadores de acceso: public, protected, default (o package-private) y private. A continuación, se describen cada uno de ellos junto con ejemplos de su uso.

<hr>

#### Public

> El modificador public hace que un miembro (variable, método, clase) sea accesible desde cualquier otra clase en el mismo paquete o en otro paquete.


```markdown
```java 
public class ClasePublic {
    public int varPublica = 10;
    public void publicMethod() {
        System.out.println("Este método es público");
    }
}
```

#### Protected

> El modificador protected hace que un miembro sea accesible dentro de su propia clase, dentro de las clases del mismo paquete y dentro de las subclases (heredadas) de esa clase, ya sea en el mismo paquete o en otro paquete.

```markdown
```java 
public class ClasePadre {
    protected int varProtegida = 20;
    protected void protectedMethod() {
        System.out.println("Este método es protected");
    }
}

public class ClaseHijadlvrja extends ClasePadre {
    public void accessProtected() {
        System.out.println("Variable protegida: " + varProtegida);
        protectedMethod();
    }
}
```

#### Default 

> Si no se especifica ningún modificador de acceso, se utiliza el modificador de acceso predeterminado, también conocido como package-private. Los miembros con este modificador son accesibles solo dentro del mismo paquete.

```markdown
```java 
class ClaseDefault {
    int varDefault = 30;
    void defaultMethod() {
        System.out.println("Este método es default");
    }
}
```

#### Private

> El modificador private hace que un miembro sea accesible solo dentro de su propia clase. No es accesible desde otras clases, incluso si están en el mismo paquete o son subclases.

```markdown
```java 
public class ClasePrivate {
    private int varPrivate = 40;
    private void privateMethod() {
        System.out.println("Este método es private");
    }
}
```

<hr>

#### Conclusiones

Al aprender sobre los modificadores de acceso en Java, descubrimos cómo controlar quién puede acceder a nuestras clases, variables y métodos. Esto es esencial para escribir código más organizado y seguro. Por ejemplo, al usar "public", permitimos que cualquier clase acceda a un miembro, mientras que con "private" restringimos el acceso solo a la propia clase. Entender estos conceptos nos ayuda a escribir programas más claros y evitar errores de seguridad. Así que, al seguir practicando y aplicando estos modificadores, podemos mejorar nuestra habilidad para crear software más robusto y mantenible en Java.
