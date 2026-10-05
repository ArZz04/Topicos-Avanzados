---

title: 'DESARROLLO DE APLICACIÓN CALCULADORA EN C# CON AVALONIA UI'
description: 'REPORTE DE IMPLEMENTACIÓN, ARQUITECTURA, SERVICIO SINGLETON Y ANÁLISIS DE PRUEBAS FUNCIONALES'
pubDate: 'September 22, 2026'
heroImage: '/calcarzz/portada.png'
-----------------------------------------

**NOMBRE:** JUAN ALBERTO ARVIZU CASTILLO <br>

**SEMESTRE:** 5to Semestre <br>

**CARRERA:** INGENIERÍA EN SISTEMAS COMPUTACIONALES

<hr>

## Introducción

En este reporte se documenta el desarrollo de una aplicación de calculadora básica de escritorio implementada en **C# y .NET**, utilizando el framework **Avalonia UI** para la construcción de la interfaz gráfica de usuario (GUI).

El proyecto fue diseñado con una resolución de **540 × 720 píxeles**, utilizando una orientación vertical y una paleta visual basada principalmente en tonos turquesa y blanco. A nivel estructural, se adoptó una arquitectura modular orientada a componentes, con el objetivo de separar la interfaz gráfica de la lógica de negocio.

Para la administración centralizada del estado de la calculadora se implementó un servicio denominado `ContentFrame`, utilizando el patrón de diseño **Singleton**. Este servicio concentra los operandos, el operador seleccionado, el estado de entrada y las reglas necesarias para ejecutar las operaciones matemáticas.

El reporte presenta los requisitos funcionales del sistema, la estructura de sus componentes, el funcionamiento de la lógica de negocio, las validaciones implementadas y las pruebas funcionales realizadas. También se documentan las principales dificultades encontradas durante el desarrollo y las soluciones aplicadas.

<hr>

## Índice

1. Requisitos y especificación del proyecto
2. Arquitectura y diseño de la interfaz
3. Lógica de negocio y manejo de errores
4. Pruebas funcionales y evidencia
5. Conclusiones e informe técnico

<hr>

## Enunciado

Desarrollar una aplicación de calculadora básica de escritorio que permita resolver operaciones aritméticas elementales:

* Suma (`+`)
* Resta (`-`)
* Multiplicación (`×`)
* División (`÷`)

La aplicación debe ser capaz de procesar números enteros y decimales, permitir el borrado de caracteres, reiniciar el estado de la pantalla y validar escenarios de error comunes sin interrumpir la ejecución del sistema.

Asimismo, se requiere implementar un sistema de entrada dual mediante **clics sobre los botones de la interfaz y captura de eventos mediante el teclado físico**, garantizando una interacción fluida para el usuario.

<hr>

# 1. Requisitos y Especificación del Proyecto

## Requisitos Funcionales

### Pantalla de visualización

La aplicación debe presentar en tiempo real el número ingresado, la operación en curso y los resultados obtenidos. La información se muestra mediante un componente visual orientado hacia la derecha.

### Entrada de datos

El sistema permite:

* Digitación de números del `0` al `9`.
* Selección de operadores aritméticos:

  * `+`
  * `-`
  * `×`
  * `÷`
* Inserción de punto decimal (`.`) para trabajar con números de punto flotante.

### Control de comandos

La calculadora incorpora los siguientes comandos:

* **`=`** — Ejecuta el cálculo de la operación actual.
* **`C`** — Limpia la pantalla y restablece el estado interno.
* **`←`** — Elimina el último carácter ingresado.

### Manejo de errores

Se implementaron mecanismos de validación para evitar situaciones que puedan generar resultados incorrectos o comportamientos inesperados:

* Detección y bloqueo de la división entre cero.
* Restricción de múltiples puntos decimales dentro de un mismo número.
* Control de operadores consecutivos.
* Manejo del borrado cuando el display se encuentra vacío o contiene un único carácter.

### Soporte de teclado

La aplicación permite utilizar el teclado físico mediante la captura de eventos `KeyDown`, haciendo posible ingresar números y comandos sin depender exclusivamente de los botones de la interfaz.

<hr>

# 2. Arquitectura y Diseño de la Interfaz

Para evitar concentrar toda la lógica en la ventana principal, el proyecto se organizó mediante componentes independientes. Esta estructura permite separar las responsabilidades de la interfaz gráfica y la lógica de procesamiento.

## Estructura del proyecto

```text
CalculArZz/
├── Components/
│   ├── Button/
│   │   ├── CustomButton.axaml
│   │   └── CustomButton.axaml.cs
│   │
│   ├── CalculatorButtons/
│   │   ├── CalculatorButtons.axaml
│   │   └── CalculatorButtons.axaml.cs
│   │
│   └── Frame/
│       ├── TopFrame.axaml
│       └── TopFrame.axaml.cs
│
├── Services/
│   └── ContentFrame.cs
│
├── MainWindow.axaml
└── MainWindow.axaml.cs
```

## Tabla comparativa de componentes UI

| Componente            | Control / Módulo         | Función / Descripción                                                                                                                |
| :-------------------- | :----------------------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| **TopFrame**          | `Border` / `TextBlock`   | Componente encargado de mostrar el historial de operaciones y el valor actual de la calculadora, con alineación hacia la derecha.    |
| **CustomButton**      | `UserControl` / `Button` | Componente reutilizable que permite establecer propiedades visuales como color de fondo, tipografía y comportamiento de los botones. |
| **CalculatorButtons** | `Grid`                   | Organiza los botones numéricos, operadores y comandos de la calculadora mediante una distribución de rejilla.                        |
| **ContentFrame**      | Servicio C# / Singleton  | Centraliza el estado de la calculadora y administra los operandos, operadores, entrada de números y reglas de cálculo.               |

## Paleta de colores y estilo visual

La interfaz utiliza una paleta basada principalmente en tonos turquesa, pastel y blanco.

La ventana tiene unas dimensiones de **540 × 720 píxeles**, proporcionando una distribución vertical adecuada para los controles de la calculadora.

| Elemento                | Color                 | Uso                                                |
| :---------------------- | :-------------------- | :------------------------------------------------- |
| **Fondo principal**     | `#FFFFFF`             | Fondo general de la aplicación.                    |
| **Pantalla**            | `#B3ECF1`             | Área donde se muestran los valores y operaciones.  |
| **Texto de pantalla**   | `#136163`             | Contraste sobre el fondo pastel.                   |
| **Botones numéricos**   | `#F4FCFC`             | Botones correspondientes a los números.            |
| **Operadores**          | `#1A98A6`             | Botones de suma, resta, multiplicación y división. |
| **Acciones especiales** | `#136163` / `#53D1DF` | Botones de igual, limpiar y borrar.                |

<hr>

# 3. Lógica de Negocio y Manejo de Errores

## Control de estado

El servicio `ContentFrame.cs` concentra el estado principal de la calculadora. De esta manera, los componentes visuales no necesitan administrar directamente los operandos ni las reglas matemáticas.

Una representación simplificada de la estructura utilizada es la siguiente:

```csharp
public class ContentFrame
{
    public static ContentFrame Instance { get; } = new();

    private double _operando1 = 0;
    private double _operando2 = 0;
    private string _operador = "";
    private bool _nuevoNumero = true;

    private string _displayText = "0";
    private string _historyText = string.Empty;

    public event Action? ContentChanged;
}
```

El patrón **Singleton** permite mantener una única instancia de `ContentFrame` durante la ejecución de la aplicación. Esto facilita que los diferentes componentes de la interfaz puedan consultar y modificar el estado centralizado de la calculadora.

## Matriz de validaciones y respuestas a errores

| Tipo de evento                   | Condición detectada                                                           | Acción tomada por el sistema                                                                                 |
| :------------------------------- | :---------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------- |
| **División por cero**            | Se presiona `=` cuando el operador es `÷` y `_operando2` tiene valor `0`.     | Se bloquea la operación, se muestra el mensaje `Error: Div / 0` y se restablece el estado de la calculadora. |
| **Punto decimal duplicado**      | Se intenta ingresar `.` cuando el número actual ya contiene un punto decimal. | La entrada es ignorada mediante la validación `!Contains(".")`.                                              |
| **Operador repetido**            | Se seleccionan varios operadores consecutivamente.                            | Se actualiza `_operador` con el último operador seleccionado sin modificar `_operando1`.                     |
| **Borrado de un único carácter** | Se presiona `←` cuando el display contiene un solo dígito o el valor `0`.     | El display se restablece a `0` y `_nuevoNumero` se establece nuevamente en `true`.                           |

## Flujo general de una operación

El procesamiento de una operación sigue una secuencia basada en el estado actual de la calculadora:

1. El usuario introduce el primer número.
2. El usuario selecciona un operador.
3. El primer operando se almacena en `_operando1`.
4. Se activa el estado de captura del segundo número.
5. El usuario introduce el segundo operando.
6. Al presionar `=`, se ejecuta la operación correspondiente.
7. El resultado se almacena nuevamente en `_operando1`.
8. El resultado se muestra en el `TopFrame`.
9. El sistema queda preparado para continuar con una nueva operación.

<hr>

# 4. Pruebas Funcionales y Evidencia

Las pruebas funcionales tuvieron como objetivo comprobar que las principales funciones de la aplicación respondieran correctamente ante entradas normales y escenarios de error.

## 4.1 Evidencia de la interfaz principal

En la siguiente evidencia se muestra la interfaz principal de la calculadora, incluyendo la pantalla de visualización, los botones numéricos, operadores y comandos especiales.

<div align="center">

**Figura 1. Interfaz principal de la calculadora**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/calcarzz/main.png" alt="Interfaz principal de la calculadora" width="400">

*Fuente: Elaboración propia.*

</div>

---

## 4.2 Prueba de ingreso de números y operaciones

Para comprobar el funcionamiento de la entrada de datos, se realizó una operación aritmética utilizando los botones de la interfaz.

La prueba permitió verificar:

* El ingreso correcto de números.
* La concatenación de varios dígitos.
* La selección de operadores.
* La actualización de la pantalla.
* La obtención del resultado mediante el botón `=`.

<div align="center">

**Figura 2. Prueba de operación aritmética**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/calcarzz/arit.png" alt="Prueba de operación aritmética" width="400">
<img src="/calcarzz/arit2.png" alt="Prueba de operación aritmética" width="400">

*Fuente: Elaboración propia.*

</div>

---

## 4.3 Recurso 1.0 — Uso de la funcionalidad de borrado

Se realizó una prueba de la función **Backspace (`←`)**, ingresando varios caracteres y eliminándolos de manera secuencial.

La prueba permitió verificar que:

* El último carácter ingresado fuera eliminado correctamente.
* La operación pudiera ejecutarse mediante el botón de la interfaz.
* El comportamiento también estuviera disponible mediante el teclado.
* El display regresara a `0` al eliminar completamente el contenido.

<div align="center">

**Figura 3. Prueba de eliminación de caracteres mediante Backspace**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/calcarzz/befdel.png" alt="Prueba de Backspace" width="400">
<img src="/calcarzz/aftdel.png" alt="Prueba de Backspace" width="400">

*Fuente: Elaboración propia.*

</div>

---

## 4.4 Recurso 1.1 — Captura de error por división entre cero

Se realizó una prueba introduciendo la operación:

```text
8 ÷ 0
```

Posteriormente se presionó el botón `=`.

El sistema detectó que el segundo operando tenía valor `0` y evitó ejecutar la división. En lugar de producir un resultado inválido, se mostró el mensaje:

```text
Error: Div / 0
```

<div align="center">

**Figura 4. Manejo de división entre cero**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/calcarzz/div0.png" alt="Error de división entre cero" width="400">
<img src="/calcarzz/er0.png" alt="Error de división entre cero" width="400">

*Fuente: Elaboración propia.*

</div>

---

## 4.5 Prueba de punto decimal

Se comprobó que la calculadora permitiera trabajar con números decimales y, al mismo tiempo, evitara la introducción de más de un punto decimal dentro del mismo número.

Por ejemplo:

```text
5.25
```

es una entrada válida, mientras que una entrada como:

```text
5.2.5
```

es rechazada parcialmente por la validación implementada.

<div align="center">

**Figura 5. Prueba de números decimales**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/calcarzz/fivedot.png" alt="Prueba de números decimales" width="400">

*Fuente: Elaboración propia.*

</div>

---

## 4.6 Prueba de limpieza de la calculadora

Mediante el botón `C` se comprobó que el sistema pudiera eliminar el contenido actual y restablecer las variables internas.

Después de ejecutar la acción, el display vuelve a mostrar:

```text
0
```

<div align="center">

**Figura 6. Prueba del botón de limpieza**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/calcarzz/cero.png" alt="Prueba del botón C" width="400">

*Fuente: Elaboración propia.*

</div>

---

## 4.7 Prueba de entrada mediante teclado

Además de la interacción mediante los botones de la interfaz, se comprobó el funcionamiento de la captura de eventos `KeyDown`.

Esta funcionalidad permite utilizar el teclado físico para introducir números y ejecutar las operaciones disponibles.

<div align="center">

**Figura 7. Prueba de entrada mediante teclado**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/calcarzz/keys.png" alt="Prueba de teclado" width="400">

*Fuente: Elaboración propia.*

</div>


<hr>

# 5. Código Fuente

El código fuente de la aplicación se encuentra disponible mediante un **GitHub Gist**, lo que permite consultar directamente los archivos utilizados durante el desarrollo.

<script src="https://gist.github.com/ArZz04/1244e6b8fa73a3d66899f5d4ae7c6bbd.js"></script>


De esta manera, el código fuente puede visualizarse directamente dentro del reporte sin necesidad de copiar todo el contenido de los archivos en el documento.

## 5.2 Archivos incluidos en el código fuente

El Gist contiene los principales archivos utilizados para la implementación de la calculadora:

```text
CalculArZz/
├── Components/
│   ├── Button/
│   │   ├── CustomButton.axaml
│   │   └── CustomButton.axaml.cs
│   ├── CalculatorButtons/
│   │   ├── CalculatorButtons.axaml
│   │   └── CalculatorButtons.axaml.cs
│   └── Frame/
│       ├── TopFrame.axaml
│       └── TopFrame.axaml.cs
│
├── Services/
│   └── ContentFrame.cs
│
├── MainWindow.axaml
└── MainWindow.axaml.cs
```

<hr>

# 6. Conclusiones e Informe Técnico

#### Dificultades encontradas

En sí, la principal dificultad que se me presentó fue que no tengo Windows, ya que mi plataforma principal es macOS. Esto hizo que tuviera que buscar un framework que permitiera desarrollar de manera multiplataforma y, al mismo tiempo, de forma óptima.

Una ventaja fue que, al no utilizar SharpDevelop, el desarrollo me resulta más entendible, ya que necesito realizar y orquestar la mayor parte del proyecto por mi cuenta. Esto permite que, al momento de hacer crecer el proyecto, pueda saber de manera más clara dónde y qué modificar o agregar.

#### Manejo de las operaciones

No fue necesario establecer una prioridad de operadores, ya que la calculadora no utiliza paréntesis ni permite realizar expresiones complejas. Las operaciones se realizan de forma secuencial: se ingresa el primer número, se selecciona el operador y después el segundo número para obtener el resultado.
