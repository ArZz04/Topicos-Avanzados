---
title: "EXCEPCIONES"
description: "INVESTIGACION SOBRE EXCEPCIONES EN JAVA"
pubDate: "May 4, 2024"
heroImage: "https://storage.googleapis.com/bari-cloud/parques-tesistan/images/Designer%20(1).jpeg"
---

**NOMBRE** : JUAN ALBERTO ARVIZU CASTILLO <br>
**SEMESTRE** : 2do Semestre<br>
**CARRERA** : INGENIERIA EN SISTEMAS COMPUTACIONALES

<hr>

#### Descripcion

Las excepciones en Java son eventos que ocurren durante la ejecución de un programa y que interrumpen el flujo normal de las instrucciones. Se utilizan para manejar errores y otros eventos excepcionales de una manera controlada. Las excepciones son objetos que representan condiciones de error y se generan (lanzan) cuando ocurre una situación inesperada.

<hr>

#### Tipos de Excepciones en Java

1. > ArithmeticException: <br> 
`Descripción`: Se lanza cuando se produce un error aritmético, como la división por cero. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    int resultado = 10 / 0; // Lanza ArithmeticException
    ```

2. > NullPointerException: <br>
`Descripción`: Se lanza cuando se intenta utilizar una referencia nula. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    String texto = null;
    int longitud = texto.length(); // Lanza NullPointerException
    ```

3. > ArrayIndexOutOfBoundsException: <br>
`Descripción`: Se lanza cuando se intenta acceder a un índice fuera de los límites de una matriz. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    int[] numeros = {1, 2, 3};
    int numero = numeros[5]; // Lanza ArrayIndexOutOfBoundsException
    ```

4. > FileNotFoundException: <br>
`Descripción`: Se lanza cuando se intenta abrir un archivo que no existe. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    File archivo = new File("archivo_no_existente.txt");
    FileReader fr = new FileReader(archivo); // Lanza FileNotFoundException
    ```

5. > IOException: <br>
`Descripción`: Se lanza cuando ocurre un error de E/S. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    FileReader fr = new FileReader("archivo.txt");
    fr.read(); // Puede lanzar IOException
    ```

6. > ClassNotFoundException: <br>
`Descripción`: Se lanza cuando no se puede encontrar una clase en tiempo de ejecución. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    Class.forName("com.example.ClaseInexistente"); // Lanza ClassNotFoundException
    ```

7. > SQLException: <br>
`Descripción`: Se lanza cuando ocurre un error al acceder a una base de datos. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    Connection conn = DriverManager.getConnection(url, user, password);
    Statement stmt = conn.createStatement();
    ResultSet rs = stmt.executeQuery("SELECT * FROM tabla_no_existente"); // Puede lanzar SQLException
    ```

8. > NumberFormatException: <br>
`Descripción`: Se lanza cuando se intenta convertir una cadena en un número y la cadena no tiene el formato adecuado. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    int numero = Integer.parseInt("abc"); // Lanza NumberFormatException
    ```

9. > IllegalArgumentException: <br>
`Descripción`: Se lanza cuando un método recibe un argumento inválido. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    Thread hilo = new Thread();
    hilo.setPriority(100); // Lanza IllegalArgumentException
    ```

10. > IllegalStateException: <br>
`Descripción`: Se lanza cuando el estado de una aplicación no es apropiado para la operación solicitada. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    List<String> lista = new ArrayList<>();
    Iterator<String> iterador = lista.iterator();
    iterador.next(); // Lanza IllegalStateException
    ```

11. > UnsupportedOperationException: <br>
`Descripción`: Se lanza cuando se intenta realizar una operación no soportada. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    List<String> listaInmutable = Collections.unmodifiableList(new ArrayList<>());
    listaInmutable.add("Elemento"); // Lanza UnsupportedOperationException
    ```

12. > ConcurrentModificationException: <br>
`Descripción`: Se lanza cuando se detecta una modificación concurrente de un objeto cuando no está permitida. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    List<String> lista = new ArrayList<>();
    lista.add("A");
    lista.add("B");
    for (String elemento : lista) {
        lista.remove(elemento); // Lanza ConcurrentModificationException
    }
    ```

13. > ClassCastException: <br>
`Descripción`: Se lanza cuando se intenta convertir un objeto a una clase a la que no pertenece. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    Object obj = new Integer(100);
    String str = (String) obj; // Lanza ClassCastException
    ```

14. > NegativeArraySizeException: <br>
`Descripción`: Se lanza cuando se intenta crear una matriz con un tamaño negativo. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    int[] matriz = new int[-5]; // Lanza NegativeArraySizeException
    ```

15. > IndexOutOfBoundsException: <br>
`Descripción`: Se lanza cuando un índice de una lista o matriz está fuera de rango. <br>
`Ejemplo`: 
    ```markdown
    ```java 
    List<String> lista = new ArrayList<>();
    lista.add("A");
    String elemento = lista.get(2); // Lanza IndexOutOfBoundsException
    ```

<hr>

#### Ejemplos

Incluidos ya en los mismos incisos
