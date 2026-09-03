---
title: "ARRAYLIST"
description: "INVESTIGACION SOBRE ARRAYLIST EN JAVA"
pubDate: "May 26, 2024"
heroImage: "https://www.boardinfinity.com/blog/content/images/2023/03/Array-vs-ArrayList.png"
---

**NOMBRE** : JUAN ALBERTO ARVIZU CASTILLO <br>
**SEMESTRE** : 2do Semestre<br>
**CARRERA** : INGENIERIA EN SISTEMAS COMPUTACIONALES

<hr>

#### Descripcion

ArrayList es una clase en Java que implementa la interfaz List y proporciona una implementación de tamaño dinámico de la matriz. Permite almacenar elementos de forma dinámica y proporciona operaciones como agregar, eliminar, recuperar y buscar elementos en la lista. La clase ArrayList es parte del paquete java.util y es una de las estructuras de datos más utilizadas en Java debido a su flexibilidad y facilidad de uso.

<hr>

#### Sintaxis

```markdown
```java 
ArrayList<Tipo> arrayListEjemplo = new ArrayList<>();
```

<hr>

#### Métodos Comunes

1. > add(E elemento): <br> 
`Descripción`: Agrega el elemento especificado al final de la lista. <br>
`Sintaxis`: public boolean add(E elemento) <br>
`Ejemplo`: 
    ```markdown
    ```java 
    ArrayList<String> listaFrutas = new ArrayList<>();
    listaFrutas.add("Manzana");
    listaFrutas.add("Banana");
    listaFrutas.add("Fresa");
    ```

2. > get(int índice): <br>
`Descripción`: Devuelve el elemento en la posición especificada en la lista. <br>
`Sintaxis`: public E get(int índice) <br>
`Ejemplo`: 
    ```markdown
    ```java 
    // Continuamos con el ejemplo del ejemplo anterior :3
    String fruta = listaFrutas.get(1); // Devuelve "Banana"
    ```

3. > remove(int índice): <br>
`Descripción`: Elimina el elemento en la posición especificada en la lista. <br>
`Sintaxis`: public E remove(int índice) <br>
`Ejemplo`: 
    ```markdown
    ```java 
    // Continuamos con el ejemplo del ejemplo anterior :3
    String frutaEliminada = listaFrutas.remove(0); // Elimina la primera fruta y devuelve "Manzana"
    ```

4. > size(): <br>
`Descripción`: Devuelve el número de elementos en la lista. <br>
`Sintaxis`: public int size() <br>
`Ejemplo`: 
    ```markdown
    ```java 
    // Continuamos con el ejemplo del ejemplo anterior :3
    int tamaño = listaFrutas.size(); // Devuelve 2 (si se ha eliminado "Manzana")
    ```

5. > contains(Object elemento): <br>
`Descripción`: Verifica si la lista contiene el elemento especificado. <br>
`Sintaxis`: public boolean contains(Object elemento) <br>
`Ejemplo`: 
    ```markdown
    ```java 
    // Continuamos con el ejemplo del ejemplo anterior :3
    boolean contieneBanana = listaFrutas.contains("Banana"); // Devuelve true
    ```

<hr>

#### Ejemplos

Incluidos ya en los mismos incisos
