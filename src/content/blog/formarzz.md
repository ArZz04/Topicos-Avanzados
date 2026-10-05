---
title: 'DESARROLLO DE APLICACIÓN DE REGISTRO DE USUARIOS EN C# CON AVALONIA UI'

description: 'REPORTE DE IMPLEMENTACIÓN, ARQUITECTURA, COMPONENTES, VALIDACIONES, BÚSQUEDA Y PRUEBAS FUNCIONALES'

pubDate: 'October 4, 2026'

heroImage: '/formarzz/init.png'

---

**NOMBRE:** JUAN ALBERTO ARVIZU CASTILLO <br>

**SEMESTRE:** 5to Semestre <br>

**CARRERA:** INGENIERÍA EN SISTEMAS COMPUTACIONALES

<hr>

# Introducción

En este reporte se documenta el desarrollo de una aplicación de escritorio para el **registro y administración de usuarios**, implementada en **C# y .NET**, utilizando el framework multiplataforma **Avalonia UI** para la construcción de la interfaz gráfica.

La aplicación fue desarrollada como una alternativa multiplataforma para trabajar con interfaces de escritorio utilizando C#, debido a que el entorno principal de desarrollo utilizado fue **macOS**. En lugar de depender exclusivamente de Windows Forms, se utilizó Avalonia UI para construir una aplicación compatible con diferentes sistemas operativos.

El proyecto permite registrar información personal de usuarios, incluyendo nombre, apellidos, correo electrónico, fecha de nacimiento, género, nacionalidad, aficiones y estado civil.

A nivel estructural, el proyecto fue dividido en diferentes componentes y servicios. La interfaz principal utiliza archivos `.axaml`, mientras que la lógica de la aplicación se distribuye entre modelos, servicios y componentes reutilizables.

Entre las principales funcionalidades implementadas se encuentran:

- Registro de usuarios.
- Edición de usuarios.
- Eliminación de usuarios.
- Limpieza del formulario.
- Validación de información.
- Validación de edad mínima.
- Validación de aceptación de términos.
- Búsqueda de usuarios en tiempo real.
- Filtrado por nombre, apellidos, correo o nacionalidad.
- Generación automática de usuarios ficticios.
- Visualización de usuarios mediante una tabla.
- Edición mediante doble clic sobre la tabla.
- Barra de progreso durante el registro.
- Ventanas de diálogo para mostrar mensajes.

El objetivo de este reporte es explicar la arquitectura utilizada, la función de cada componente, la lógica implementada y las pruebas realizadas sobre la aplicación.

<hr>

# Índice

1. Requisitos y especificación del proyecto
2. Arquitectura y diseño de la interfaz
3. Modelos y lógica de negocio
4. Servicios y validaciones
5. Funcionalidad de búsqueda y administración de usuarios
6. Pruebas funcionales y evidencia
7. Código fuente
8. Conclusiones e informe técnico

<hr>

# 1. Requisitos y Especificación del Proyecto

## Requisitos funcionales

La aplicación fue desarrollada para permitir el registro y administración de información de usuarios mediante una interfaz gráfica de escritorio.

### Registro de información personal

El formulario permite capturar los siguientes datos:

- Nombre.
- Apellido paterno.
- Apellido materno.
- Correo electrónico.
- Fecha de nacimiento.
- Nacionalidad.
- Género.
- Aficiones.
- Estado civil.
- Aceptación de términos y condiciones.

Los campos de nombre, apellidos y correo se implementan mediante un componente reutilizable denominado `InputFieldView`, mientras que la fecha de nacimiento utiliza un `CalendarDatePicker` y la nacionalidad un `ComboBox`.

### Selección de aficiones

La aplicación permite seleccionar diferentes aficiones mediante controles `CheckBox`.

Actualmente se encuentran disponibles:

- Leer.
- Ir al cine.

Estos valores se almacenan en propiedades booleanas dentro del modelo del usuario.

### Selección de género

El género se selecciona mediante `RadioButton`.

Las opciones disponibles son:

- Masculino.
- Femenino.

Los botones utilizan el mismo `GroupName`, permitiendo que solamente una opción sea seleccionada.

### Estado civil

También se implementó una selección mediante `RadioButton` para determinar el estado civil del usuario:

- Soltero.
- Casado.

Internamente se utiliza la propiedad booleana `IsCasado`.

### Términos y condiciones

Antes de registrar un usuario es necesario activar la opción:

`Acepto que leí los términos y condiciones`

El estado de esta opción se almacena mediante la propiedad `AceptoTerminos`.

### Administración de usuarios

La aplicación permite realizar las siguientes operaciones:

| Operación | Descripción |
| :--- | :--- |
| **Guardar** | Registra un nuevo usuario o actualiza uno existente. |
| **Limpiar** | Restablece el formulario y elimina la selección actual. |
| **Eliminar seleccionado** | Elimina el usuario seleccionado de la tabla. |
| **Generar 10 usuarios** | Crea diez usuarios ficticios automáticamente. |
| **Editar** | Permite modificar un usuario mediante doble clic sobre la tabla. |
| **Buscar** | Filtra usuarios conforme se escribe en el buscador. |

Los comandos principales son definidos en el `MainViewModel`.

<hr>

# 2. Arquitectura y Diseño de la Interfaz

Para evitar concentrar toda la lógica dentro de la ventana principal, el proyecto fue dividido en diferentes módulos.

La estructura permite separar:

- Modelos de datos.
- Lógica de negocio.
- Servicios.
- Componentes visuales.
- Ventanas.
- Interfaz principal.

## Estructura del proyecto

```text
WindowsForm/

├── MainWindow.axaml
├── MainWindow.axaml.cs
│
├── models/
│   ├── UserModel.cs
│   └── MainViewModel.cs
│
├── services/
│   ├── UserService.cs
│   ├── RelayCommand.cs
│   ├── UserGeneratorService.cs
│   ├── UserValidator.cs
│   └── DialogService.cs
│
└── components/
    ├── ButtonView/
    │   ├── ButtonView.axaml
    │   └── ButtonView.axaml.cs
    │
    ├── InputFieldView/
    │   ├── InputFieldView.axaml
    │   └── InputFieldView.axaml.cs
    │
    ├── MultipleCheckView/
    │   ├── MultipleCheckView.axaml
    │   └── MultipleCheckView.axaml.cs
    │
    ├── SingleCheckView/
    │   ├── SingleCheckView.axaml
    │   └── SingleCheckView.axaml.cs
    │
    ├── UsersTableView/
    │   ├── UsersTableView.axaml
    │   └── UsersTableView.axaml.cs
    │
    └── EditUserWindow/
        ├── EditUserWindow.axaml
        └── EditUserWindow.axaml.cs
```

La ventana principal integra los diferentes componentes visuales mediante controles reutilizables. Por ejemplo, `InputFieldView`, `MultipleCheckView`, `SingleCheckView`, `ButtonView` y `UsersTableView` son incorporados desde `MainWindow.axaml`.

## Tabla comparativa de componentes

| Componente | Control / Módulo | Función |
| :--- | :--- | :--- |
| **InputFieldView** | `UserControl` / `TextBox` | Captura texto para los datos personales. |
| **MultipleCheckView** | `CheckBox` | Permite seleccionar las aficiones. |
| **SingleCheckView** | `RadioButton` / `CheckBox` | Administra género, estado civil y términos. |
| **ButtonView** | `Button` / `ProgressBar` | Contiene las acciones principales del formulario. |
| **UsersTableView** | `DataGrid` / `TextBox` | Muestra usuarios y permite buscarlos. |
| **EditUserWindow** | `Window` | Permite editar los datos de un usuario. |
| **UserModel** | Clase C# | Representa la información de cada usuario. |
| **MainViewModel** | ViewModel | Coordina el formulario, comandos, búsqueda y operaciones. |
| **UserService** | Servicio C# | Administra la colección de usuarios. |
| **UserValidator** | Servicio C# | Valida los datos antes de guardar. |
| **UserGeneratorService** | Servicio C# | Genera usuarios ficticios automáticamente. |
| **DialogService** | Servicio C# | Muestra mensajes mediante ventanas de diálogo. |

## Diseño de la ventana principal

La ventana principal tiene unas dimensiones de:

```text
700 × 780 píxeles
```

La interfaz utiliza un fondo gris claro y diferentes áreas delimitadas mediante `Border`.

La información se distribuye principalmente en cuatro secciones:

1. Datos personales.
2. Opciones del usuario.
3. Botones y carga.
4. Tabla de usuarios.

El diseño utiliza un `ScrollViewer`, permitiendo desplazarse verticalmente cuando el contenido excede el área visible.

## Paleta visual

La aplicación utiliza una interfaz sencilla basada principalmente en tonos claros.

| Elemento | Color | Uso |
| :--- | :--- | :--- |
| **Fondo principal** | `#EAEAEA` | Fondo general de la ventana. |
| **Paneles** | `#F2F2F2` | Separación visual de secciones. |
| **Campos** | `White` | Entrada de información. |
| **Texto principal** | `#222222` / `#111111` | Información y etiquetas. |
| **Bordes** | `#A0A0A0` / `#B0B0B0` | Delimitación de controles. |
| **Botón principal** | `#E0E0E0` | Acciones generales. |
| **Botón de edición** | `#005A9E` | Acción de guardar cambios. |

<hr>

# 3. Modelos y Lógica de Negocio

## Modelo `UserModel`

La clase `UserModel` representa a cada usuario registrado dentro de la aplicación.

Entre sus propiedades se encuentran:

```csharp
public Guid Id { get; set; }

public string Nombre { get; set; }

public string ApellidoPaterno { get; set; }

public string ApellidoMaterno { get; set; }

public string Correo { get; set; }

public DateTime? FechaNacimiento { get; set; }

public bool AficionLeer { get; set; }

public bool AficionCine { get; set; }

public string Genero { get; set; }

public string Nacionalidad { get; set; }

public bool IsCasado { get; set; }
```

Cada usuario recibe automáticamente un identificador único mediante `Guid.NewGuid()`.

## Propiedades calculadas

El modelo también contiene propiedades utilizadas para presentar información de una manera más amigable.

Para las aficiones se utiliza:

```csharp
public string AficionesTexto
```

Esta propiedad convierte los valores booleanos en texto:

| Leer | Cine | Resultado |
| :---: | :---: | :--- |
| Sí | Sí | Leer, Ir al cine |
| Sí | No | Leer |
| No | Sí | Ir al cine |
| No | No | Ninguna |

De manera similar, `EstadoCivilTexto` convierte el valor booleano de `IsCasado` en:

```text
Casado
```

o

```text
Soltero
```



## MainViewModel

El `MainViewModel` funciona como uno de los principales puntos de coordinación de la aplicación.

Dentro de este ViewModel se administran:

- El usuario que se encuentra actualmente en el formulario.
- La lista de nacionalidades.
- El usuario seleccionado.
- La aceptación de términos.
- El texto de búsqueda.
- La lista filtrada.
- El estado de carga.
- El progreso.
- Los comandos de la interfaz.

También implementa `INotifyPropertyChanged` para notificar cambios a la interfaz.

## Comandos

Los botones de la aplicación utilizan comandos:

```csharp
RegistrarCommand
LimpiarCommand
EliminarCommand
GenerarAleatoriosCommand
```

Estos comandos se inicializan utilizando `RelayCommand`.

```csharp
RegistrarCommand =
    new RelayCommand(async () => await GuardarAsync());

LimpiarCommand =
    new RelayCommand(LimpiarFormulario);

EliminarCommand =
    new RelayCommand(Eliminar);

GenerarAleatoriosCommand =
    new RelayCommand(GenerarUsuariosAleatorios);
```

Esto permite separar la acción visual del procesamiento correspondiente.

<hr>

# 4. Servicios y Validaciones

## UserService

El servicio `UserService` administra la colección de usuarios mediante un `ObservableCollection<UserModel>`.

```csharp
public ObservableCollection<UserModel> Usuarios { get; } = new();
```

La operación `GuardarOActualizar` determina si el usuario ya existe utilizando su identificador.

Si encuentra un usuario con el mismo `Id`, se realiza una actualización.

Si no existe, se agrega como un nuevo registro.

## Eliminación

La eliminación se realiza mediante:

```csharp
public void Eliminar(UserModel usuario)
{
    Usuarios.Remove(usuario);
}
```

El `MainViewModel` solamente realiza esta operación cuando existe un usuario seleccionado.

## UserValidator

Antes de registrar o actualizar un usuario, el sistema realiza una validación.

Entre las condiciones comprobadas se encuentran:

- Nombre obligatorio.
- Género obligatorio.
- Apellido paterno obligatorio.
- Apellido materno obligatorio.
- Correo obligatorio.
- Nacionalidad obligatoria.
- Fecha de nacimiento válida.
- Edad mínima de 18 años.
- Aceptación de términos y condiciones.

La validación devuelve una pareja de valores:

```csharp
(bool EsValido, string MensajeError)
```

Esto permite determinar si la información puede continuar hacia el proceso de registro.

## Validación de edad

Una de las reglas de negocio implementadas consiste en impedir el registro de personas menores de 18 años.

El sistema calcula la edad utilizando la fecha de nacimiento y la fecha actual.

Si la edad calculada es menor a 18, el registro es rechazado con el mensaje:

```text
Debe ser mayor de 18 años para registrarse.
```



## Matriz de validaciones

| Validación | Condición | Respuesta |
| :--- | :--- | :--- |
| **Nombre** | Campo vacío | Se muestra advertencia. |
| **Apellido paterno** | Campo vacío | Se muestra advertencia. |
| **Apellido materno** | Campo vacío | Se muestra advertencia. |
| **Correo** | Campo vacío | Se muestra advertencia. |
| **Género** | Sin seleccionar | Se muestra advertencia. |
| **Nacionalidad** | Sin seleccionar | Se muestra advertencia. |
| **Fecha de nacimiento** | Sin fecha | Se muestra advertencia. |
| **Edad** | Menor de 18 años | Se rechaza el registro. |
| **Términos** | No aceptados | Se rechaza el registro. |

<hr>

# 5. Funcionalidad de Búsqueda y Administración de Usuarios

## Búsqueda en tiempo real

La aplicación incorpora un campo de búsqueda asociado a la propiedad:

```csharp
TextoBusqueda
```

Cada vez que el contenido del campo cambia, se ejecuta:

```csharp
ActualizarFiltro();
```

Esto permite que los resultados de la tabla se actualicen automáticamente sin necesidad de presionar un botón adicional.

## Campos utilizados para la búsqueda

La búsqueda puede encontrar usuarios utilizando:

- Nombre.
- Apellido paterno.
- Apellido materno.
- Correo.
- Nacionalidad.

La comparación se realiza convirtiendo el texto a minúsculas y utilizando `Contains`.

```csharp
_userService.Usuarios.Where(u =>
    u.Nombre.ToLower().Contains(busqueda) ||
    u.ApellidoPaterno.ToLower().Contains(busqueda) ||
    u.ApellidoMaterno.ToLower().Contains(busqueda) ||
    u.Correo.ToLower().Contains(busqueda) ||
    u.Nacionalidad.ToLower().Contains(busqueda));
```



## Tabla de usuarios

Los usuarios se muestran mediante un `DataGrid`.

Las columnas implementadas son:

| Columna | Información |
| :--- | :--- |
| Nombre | Nombre del usuario. |
| A. Paterno | Apellido paterno. |
| A. Materno | Apellido materno. |
| Correo | Correo electrónico. |
| Género | Género seleccionado. |
| Nacionalidad | Nacionalidad seleccionada. |
| Fecha Nac. | Fecha de nacimiento. |
| Aficiones | Aficiones seleccionadas. |
| Estado Civil | Estado civil. |



## Edición mediante doble clic

La tabla permite seleccionar un usuario y abrir una ventana secundaria mediante doble clic.

El sistema crea un nuevo `MainViewModel`, copia los datos del usuario seleccionado y los carga en `EditUserWindow`.

Después de guardar los cambios, el usuario actualizado se vuelve a registrar mediante `GuardarOActualizar`.

Este mecanismo permite realizar una edición sin modificar directamente el formulario principal.

## Generación automática de usuarios

La aplicación incluye un botón denominado:

```text
Generar 10 Usuarios
```

Esta función utiliza `UserGeneratorService`.

El servicio genera automáticamente:

- Nombre.
- Apellidos.
- Correo.
- Fecha de nacimiento.
- Género.
- Nacionalidad.
- Aficiones.
- Estado civil.

Los datos son seleccionados aleatoriamente a partir de diferentes arreglos de información ficticia.

<hr>

# 6. Pruebas Funcionales y Evidencia

Las pruebas funcionales tuvieron como objetivo comprobar el funcionamiento de las principales operaciones de la aplicación, incluyendo registro, validación, búsqueda, edición y eliminación.

## 6.1 Evidencia de la interfaz principal

En esta prueba se muestra la ventana principal de la aplicación, incluyendo:

- Formulario de datos personales.
- Opciones de usuario.
- Botones.
- Barra de progreso.
- Buscador.
- Tabla de usuarios.

<div align="center">

**Figura 1. Interfaz principal de la aplicación**


<img src="/formarzz/init.png" alt="Interfaz principal" width="600">

*Fuente: Elaboración propia.*

</div>

---

## 6.2 Prueba de registro de usuario

Para comprobar el registro se introdujeron datos en los diferentes campos del formulario.

Se verificó:

- Captura del nombre.
- Captura de apellidos.
- Captura del correo.
- Selección de fecha.
- Selección de nacionalidad.
- Selección de género.
- Selección de aficiones.
- Selección del estado civil.
- Aceptación de términos.
- Registro mediante el botón **Guardar**.

Durante el proceso se muestra una barra de progreso.

<div align="center">

**Figura 2. Registro de un usuario**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/formarzz/register.png" alt="Registro de usuario" width="600">

*Fuente: Elaboración propia.*

</div>

---

## 6.3 Prueba de validación de campos

Se intentó registrar un usuario sin proporcionar todos los datos requeridos.

El sistema detectó el campo faltante y mostró una ventana de advertencia.

Por ejemplo:

```text
Por favor, ingrese el nombre.
```

La aplicación no continúa con el registro mientras la información obligatoria no haya sido completada.

<div align="center">

**Figura 3. Validación de campos obligatorios**


<img src="/formarzz/unnamed.png" alt="Validación de campos" width="500">

*Fuente: Elaboración propia.*

</div>

---

## 6.4 Prueba de edad mínima

Se realizó una prueba utilizando una fecha de nacimiento correspondiente a una persona menor de 18 años.

El sistema calcula la edad y rechaza el registro cuando no se cumple la regla de negocio.

El mensaje mostrado es:

```text
Debe ser mayor de 18 años para registrarse.
```

<div align="center">

**Figura 4. Validación de edad mínima**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/formarzz/unathorized.png" alt="Validación de edad" width="500">

*Fuente: Elaboración propia.*

</div>

---

## 6.5 Prueba de términos y condiciones

Se realizó un intento de registro sin activar la casilla correspondiente a los términos y condiciones.

El sistema detectó que la propiedad `AceptoTerminos` era falsa y rechazó el registro.

El mensaje mostrado es:

```text
Debe aceptar los términos y condiciones.
```

<div align="center">

**Figura 5. Validación de términos y condiciones**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/formarzz/untherms.png" alt="Validación de términos" width="500">

*Fuente: Elaboración propia.*

</div>

---

## 6.6 Prueba de generación automática

Se utilizó el botón:

```text
Generar 10 Usuarios
```

La aplicación generó diez registros ficticios y los agregó a la colección de usuarios.

La prueba permitió comprobar el funcionamiento de `UserGeneratorService` y la actualización automática de la tabla.

<div align="center">

**Figura 6. Generación automática de usuarios**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/formarzz/gen10.png" alt="Usuarios generados automáticamente" width="600">

*Fuente: Elaboración propia.*

</div>

---

## 6.7 Prueba de búsqueda en tiempo real

Se introdujo texto dentro del campo:

```text
Buscar usuario:
```

Conforme se escribieron los caracteres, la tabla fue filtrando los resultados.

La búsqueda permite utilizar diferentes datos del usuario, incluyendo:

- Nombre.
- Apellidos.
- Correo.
- Nacionalidad.

<div align="center">

**Figura 7. Búsqueda de usuarios**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/formarzz/search.png" alt="Búsqueda de usuarios" width="600">

*Fuente: Elaboración propia.*

</div>

---

## 6.8 Prueba de edición de usuario

Se realizó doble clic sobre un usuario de la tabla.

El sistema abrió una ventana denominada:

```text
Editar Usuario
```

Los datos del usuario seleccionado fueron cargados en el formulario secundario.

Después de modificar los datos y seleccionar **Guardar Cambios**, el usuario fue actualizado en la lista principal.

<div align="center">

**Figura 8. Edición de usuario**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/formarzz/edituser.png" alt="Edición de usuario" width="600">

*Fuente: Elaboración propia.*

</div>

---

## 6.9 Prueba de eliminación

Se seleccionó un usuario dentro de la tabla y posteriormente se utilizó el botón:

```text
Eliminar Seleccionado
```

El usuario fue eliminado de la colección administrada por `UserService`.

<div align="center">

**Figura 9. Eliminación de usuario**

<!-- INSERTAR CAPTURA AQUÍ -->

<img src="/formarzz/del1.png" alt="Eliminación de usuario" width="600">
<img src="/formarzz/del2.png" alt="Eliminación de usuario" width="600">

*Fuente: Elaboración propia.*

</div>

<hr>

# 7. Código Fuente

El proyecto está compuesto por archivos C# y XAML/AXAML distribuidos en diferentes módulos.

## Archivos principales

```text
WindowsForm/

├── MainWindow.axaml
├── MainWindow.axaml.cs
│
├── models/
│   ├── UserModel.cs
│   └── MainViewModel.cs
│
├── services/
│   ├── UserService.cs
│   ├── RelayCommand.cs
│   ├── UserGeneratorService.cs
│   ├── UserValidator.cs
│   └── DialogService.cs
│
└── components/
    ├── ButtonView/
    ├── InputFieldView/
    ├── MultipleCheckView/
    ├── SingleCheckView/
    ├── UsersTableView/
    └── EditUserWindow/
```

## Función de los archivos

| Archivo | Responsabilidad |
| :--- | :--- |
| `MainWindow.axaml` | Define la interfaz principal. |
| `MainWindow.axaml.cs` | Inicializa la ventana principal. |
| `UserModel.cs` | Define la estructura de un usuario. |
| `MainViewModel.cs` | Coordina el formulario y las operaciones. |
| `UserService.cs` | Administra la colección de usuarios. |
| `RelayCommand.cs` | Implementa los comandos de la interfaz. |
| `UserGeneratorService.cs` | Genera usuarios ficticios. |
| `UserValidator.cs` | Valida la información capturada. |
| `DialogService.cs` | Genera ventanas de alerta. |
| `InputFieldView` | Componente reutilizable para entradas de texto. |
| `MultipleCheckView` | Componente para las aficiones. |
| `SingleCheckView` | Componente para género y estado civil. |
| `ButtonView` | Contiene los botones principales. |
| `UsersTableView` | Muestra y busca usuarios. |
| `EditUserWindow` | Permite editar un usuario. |

<hr>

# 8. Conclusiones e Informe Técnico

## Dificultades encontradas

Una de las principales dificultades durante el desarrollo fue trabajar con una interfaz de escritorio en C# utilizando **macOS**, ya que la tecnología tradicional de Windows Forms está orientada al ecosistema de Windows.

Debido a esto, se utilizó **Avalonia UI**, permitiendo desarrollar una aplicación de escritorio con C# y XAML/AXAML sin depender exclusivamente de Windows.

Otra dificultad fue organizar la aplicación de manera que la lógica no quedara concentrada completamente en la ventana principal.

Para resolverlo, se dividió el proyecto en modelos, servicios y componentes reutilizables.

## Arquitectura utilizada

La separación realizada permitió que cada parte del sistema tuviera una responsabilidad específica.

Por ejemplo:

- `UserModel` representa los datos.
- `MainViewModel` coordina el estado de la interfaz.
- `UserService` administra los registros.
- `UserValidator` controla las reglas de validación.
- `UserGeneratorService` genera datos ficticios.
- `DialogService` administra mensajes.
- Los componentes `UserControl` contienen partes específicas de la interfaz.

Esta organización facilita realizar modificaciones posteriores sin tener que modificar toda la aplicación.

## Búsqueda en tiempo real

Una de las funcionalidades más importantes agregadas al proyecto fue el buscador.

El campo de búsqueda modifica `TextoBusqueda`, y cada modificación provoca que se ejecute `ActualizarFiltro()`.

De esta manera, la tabla muestra únicamente los usuarios que coinciden con el texto ingresado.

Esta funcionalidad también permite demostrar el uso de `ObservableCollection`, eventos de cambio y binding entre la interfaz y el ViewModel.

## Administración de usuarios

El proyecto no solamente permite registrar información, sino también administrar los registros existentes.

Las operaciones implementadas son:

```text
Crear
Leer
Actualizar
Eliminar
```

La información se mantiene actualmente en memoria mediante `ObservableCollection<UserModel>`.

Por lo tanto, los registros no representan todavía una persistencia en una base de datos externa.

## Generación de datos

La implementación de `UserGeneratorService` permite generar información ficticia para probar la aplicación rápidamente.

Esto resulta especialmente útil para comprobar:

- El comportamiento del `DataGrid`.
- El buscador.
- La selección de usuarios.
- La edición.
- La eliminación.
- El comportamiento con múltiples registros.

## Conclusión

El desarrollo permitió implementar una aplicación de escritorio funcional utilizando **C#, .NET y Avalonia UI**, aplicando una estructura modular para separar la interfaz, los modelos y la lógica de negocio.

La aplicación cuenta con registro de usuarios, validaciones, búsqueda en tiempo real, edición, eliminación y generación automática de información ficticia.

Además, el uso de componentes reutilizables permite que diferentes partes del formulario puedan mantenerse de manera independiente.

Como resultado, se obtuvo una aplicación multiplataforma funcional que demuestra el manejo de interfaces gráficas, binding, comandos, modelos, servicios, validaciones y administración de colecciones en C#.

---