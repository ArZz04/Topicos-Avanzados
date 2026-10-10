---
title: 'Desarrollo de un editor de texto simple en C# con Avalonia UI'
description: 'Reporte de implementación: arquitectura modular, componentes reutilizables, manejo de archivos, formato y pruebas funcionales'
pubDate: 2026-10-09
heroImage: '/editorarzz/main.png'
---
**NOMBRE:** JUAN ALBERTO ARVIZU CASTILLO <br>
**SEMESTRE:** 5to Semestre <br>
**CARRERA:** INGENIERÍA EN SISTEMAS COMPUTACIONALES

<hr>

# Introducción

En este reporte se documenta el desarrollo de una aplicación de escritorio correspondiente a un **Editor de Texto Simple**, implementada en **C# y .NET 10.0**, utilizando el framework multiplataforma **Avalonia UI** para la construcción de la interfaz gráfica y la arquitectura basada en componentes.

Al igual que en prácticas anteriores, la aplicación se desarrolló sobre un entorno de trabajo con **macOS**, lo que impulsó la elección de Avalonia UI como sustituto nativo y multiplataforma frente al clásico Windows Forms de .NET. Mediante el uso de `DockPanel` como contenedor principal, se replicó la distribución equivalente a un `BorderLayout` o `DockLayout`.

El sistema implementa las funciones esenciales de edición de texto plano: creación, apertura y guardado de archivos, operaciones del portapapeles (copiar, cortar, pegar), búsqueda de texto, personalización visual (fuente, tamaño y color de fondo) y una barra de estado dinámica con indicador de línea, columna, recuento de caracteres y cambios no guardados.

Estructuralmente, el proyecto adopta una arquitectura desacoplada organizada en módulos independientes dentro de directorios para componentes (`components/`), servicios (`services/`) y modelos (`models/`), garantizando la reutilización de código y la mantenibilidad del software.

El objetivo de este reporte es detallar la arquitectura, el funcionamiento de cada componente, las técnicas de manejo de eventos, la lógica de negocio y la validación funcional del editor.

<hr>

# Índice

1. [Requisitos y especificación del proyecto](#1-requisitos-y-especificación-del-proyecto)
2. [Arquitectura y diseño de la interfaz](#2-arquitectura-y-diseño-de-la-interfaz)
3. [Modelos y lógica de negocio](#3-modelos-y-lógica-de-negocio)
4. [Servicios y manejo de archivos](#4-servicios-y-manejo-de-archivos)
5. [Componentes visuales y ventanas secundarias](#5-componentes-visuales-y-ventanas-secundarias)
6. [Pruebas funcionales y evidencia](#6-pruebas-funcionales-y-evidencia)
7. [Código fuente](#7-código-fuente)
8. [Conclusiones e informe técnico](#8-conclusiones-e-informe-técnico)

<hr>

# 1. Requisitos y especificación del proyecto

## Requisitos funcionales

La práctica consiste en el diseño e implementación de un editor de texto funcional que incluye las operaciones estándar de manipulación de documentos de texto y personalización visual.

> **Nota sobre atajos:** En el entorno macOS declarado, el modificador `Ctrl` se sustituye por `Cmd` (`⌘`). El código maneja ambos mediante `KeyModifiers.Control` y `KeyModifiers.Meta`.

### Operaciones de archivo

- **Nuevo (Ctrl+N / ⌘N):** Limpia el área de edición y reinicia el estado de guardado del documento.
- **Abrir (Ctrl+O / ⌘O):** Carga un archivo de texto plano mediante el explorador del sistema (`StorageProvider`).
- **Guardar (Ctrl+S / ⌘S):** Guarda los cambios en la ruta actual o invoca la opción de "Guardar como" si el documento no se ha almacenado previamente.
- **Guardar como...:** Permite seleccionar la ruta y el nombre del archivo en el disco.
- **Salir (Ctrl+Q / ⌘Q):** Cierra la aplicación solicitando confirmación si existen cambios pendientes sin guardar.

### Operaciones de edición y búsqueda

- **Cortar (Ctrl+X / ⌘X):** Elimina el texto seleccionado y lo coloca en el portapapeles.
- **Copiar (Ctrl+C / ⌘C):** Copia el texto seleccionado al portapapeles.
- **Pegar (Ctrl+V / ⌘V):** Inserta el texto del portapapeles en la posición actual del cursor.
- **Buscar (Ctrl+F / ⌘F):** Despliega una ventana modal para localizar coincidencias dentro del documento y resaltar su posición.

### Formato y personalización

- **Tipo de letra:** Diálogo personalizado (`FontPickerWindow`) para cambiar la familia tipográfica (Arial, Consolas, Courier New, etc.) y su tamaño con vista previa.
- **Color de fondo:** Diálogo personalizado (`ColorPickerWindow`) con paleta de colores predefinidos para modificar el fondo del editor y adaptar dinámicamente la luminancia del texto (blanco/negro).

### Barra de estado y monitoreo

- Muestra dinámicamente la posición actual del cursor (Línea y Columna).
- Calcula el total de caracteres en tiempo real.
- Indica el estado del documento mediante etiquetas visuales (`[Guardado]` / `[Modificado]`).
- Muestra mensajes informativos sobre las acciones realizadas.

<hr>

# 2. Arquitectura y diseño de la interfaz

Para asegurar un desarrollo limpio y extensible, la aplicación fragmenta la interfaz en controles de usuario (`UserControl`) y ventanas secundarias (`Window`).

```text
TextEditArZz/
├── App.axaml
├── App.axaml.cs
├── MainWindow.axaml
├── MainWindow.axaml.cs
├── Program.cs
├── TextEditArZz.csproj
├── icons/
├── models/
│   └── EditorState.cs
├── services/
│   └── FileService.cs
└── components/
    ├── AboutWindow/
    │   ├── AboutWindow.axaml
    │   └── AboutWindow.axaml.cs
    ├── ColorPickerWindow/
    │   ├── ColorPickerWindow.axaml
    │   └── ColorPickerWindow.axaml.cs
    ├── EditorView/
    │   ├── EditorView.axaml
    │   └── EditorView.axaml.cs
    ├── FontPickerWindow/
    │   ├── FontPickerWindow.axaml
    │   └── FontPickerWindow.axaml.cs
    ├── MainMenuView/
    │   ├── MainMenuView.axaml
    │   └── MainMenuView.axaml.cs
    ├── SearchWindow/
    │   ├── SearchWindow.axaml
    │   └── SearchWindow.axaml.cs
    ├── StatusBarView/
    │   ├── StatusBarView.axaml
    │   └── StatusBarView.axaml.cs
    └── ToolBarView/
        ├── ToolBarView.axaml
        └── ToolBarView.axaml.cs
```

## Distribución con DockPanel (DockLayout / BorderLayout)

El archivo `MainWindow.axaml` emplea un contenedor `<DockPanel LastChildFill="True">` para organizar las regiones de la ventana principal:

| Componente | Posición (`DockPanel.Dock`) | Función |
| --- | --- | --- |
| **MainMenuView** | `Top` | Barra de menú superior (`Menu`, `MenuItem`). |
| **ToolBarView** | `Top` | Barra de herramientas con botones de acceso rápido e iconos. |
| **StatusBarView** | `Bottom` | Barra de estado inferior (Línea, Columna, Caracteres, Estado). |
| **EditorView** | Centro (`Fill`) | Área principal de texto multilínea (`TextBox`). |

## Tabla comparativa de componentes del proyecto

| Componente / Módulo | Tipo | Función |
| --- | --- | --- |
| **MainMenuView** | `UserControl` | Define la barra de menú con atajos e iconos integrados. |
| **ToolBarView** | `UserControl` | Réplica en barra de botones con imágenes de recursos. |
| **EditorView** | `UserControl` | Área de texto principal; calcula cursor y aplica formato. |
| **StatusBarView** | `UserControl` | Muestra métricas del documento y mensajes del sistema. |
| **SearchWindow** | `Window` | Diálogo modal para la búsqueda de texto. |
| **ColorPickerWindow** | `Window` | Diálogo modal para seleccionar el color de fondo. |
| **FontPickerWindow** | `Window` | Diálogo modal para ajustar fuente y tamaño con previsualización. |
| **AboutWindow** | `Window` | Ventana informativa sobre la aplicación. |
| **EditorState** | Clase C# | Modelo que almacena el estado del documento actual. |
| **FileService** | Servicio C# | Gestiona lectura y escritura asíncrona mediante el sistema de archivos. |

<hr>

# 3. Modelos y lógica de negocio

## Modelo `EditorState`

Representa el estado operativo del documento en edición.

```csharp
namespace TextEditArZz.Models
{
    public class EditorState
    {
        public string Content { get; set; } = string.Empty;
        public string FilePath { get; set; } = string.Empty;
        public bool IsModified { get; set; } = false;
        public int Line { get; set; } = 1;
        public int Column { get; set; } = 1;
    }
}
```

## Cálculo de posición del cursor (Línea y Columna)

Dentro de `EditorView.axaml.cs`, la posición del cursor se obtiene de forma dinámica analizando el índice `CaretIndex` dentro de la cadena global de texto:

```csharp
private void OnSelectionOrCaretChanged(object? sender, EventArgs e)
{
    int caretIndex = MainTextBox.CaretIndex;
    string text = MainTextBox.Text ?? string.Empty;

    int line = 1;
    int col = 1;

    for (int i = 0; i < caretIndex && i < text.Length; i++)
    {
        if (text[i] == '\n')
        {
            line++;
            col = 1;
        }
        else
        {
            col++;
        }
    }

    OnCursorPositionChanged?.Invoke(this, (line, col));
}
```

## Ajuste dinámico de contraste de texto

Cuando el usuario selecciona un nuevo color de fondo a través de `SetBackgroundColor(IBrush brush)`, el control analiza la luminancia relativa del color seleccionado (con corrección gamma, según la especificación WCAG 2.1) para alternar automáticamente el color de fuente entre blanco y negro, garantizando la legibilidad:

```csharp
private static double GetRelativeLuminance(Color c)
{
    static double Channel(byte v)
    {
        double s = v / 255.0;
        return s <= 0.03928 ? s / 12.92 : Math.Pow((s + 0.055) / 1.055, 2.4);
    }

    return 0.2126 * Channel(c.R) + 0.7152 * Channel(c.G) + 0.0722 * Channel(c.B);
}

public void SetBackgroundColor(IBrush brush)
{
    if (brush is ISolidColorBrush solidBrush)
    {
        var color = solidBrush.Color;
        double luminance = GetRelativeLuminance(color);
        MainTextBox.Foreground = luminance < 0.5 ? Brushes.White : Brushes.Black;
    }
}
```

<hr>

# 4. Servicios y manejo de archivos

## Clase `FileService`

Para la manipulación de archivos en disco sin bloquear la interfaz gráfica, `FileService` implementa la API de almacenamiento de Avalonia (`IStorageProvider`), permitiendo un comportamiento multiplataforma nativo (incluyendo el sandbox de macOS).

```csharp
public class FileService
{
    public async Task<string?> OpenFileAsync(Window window)
    {
        var files = await window.StorageProvider.OpenFilePickerAsync(new FilePickerOpenOptions
        {
            Title = "Abrir Archivo de Texto",
            AllowMultiple = false,
            FileTypeFilter = new[] { FilePickerFileTypes.TextPlain, FilePickerFileTypes.All }
        });

        if (files.Count > 0)
        {
            await using var stream = await files[0].OpenReadAsync();
            using var reader = new StreamReader(stream);
            return await reader.ReadToEndAsync();
        }

        return null;
    }

    public async Task SaveFileAsync(IStorageFile file, string content)
    {
        await using var stream = await file.OpenWriteAsync();
        stream.SetLength(0);
        await using var writer = new StreamWriter(stream);
        await writer.WriteAsync(content);
        await writer.FlushAsync();
    }

    public async Task<IStorageFile?> SaveFileAsAsync(Window window)
    {
        return await window.StorageProvider.SaveFilePickerAsync(new FilePickerSaveOptions
        {
            Title = "Guardar Archivo",
            DefaultExtension = "txt",
            FileTypeChoices = new[] { FilePickerFileTypes.TextPlain, FilePickerFileTypes.All }
        });
    }
}
```

<hr>

# 5. Componentes visuales y ventanas secundarias

## Gestión de la confirmación al salir

En `MainWindow.axaml.cs`, el evento `OnWindowClosing` intercepta el cierre de la ventana si la variable `_isModified` es verdadera, desplegando un cuadro de diálogo dinámico construido mediante `UniformGrid`:

```csharp
private bool _forceClose = false;

private async void OnWindowClosing(object? sender, WindowClosingEventArgs e)
{
    if (_forceClose || !_isModified) return;

    e.Cancel = true;

    var dialog = new Window
    {
        Title = "Guardar cambios",
        Width = 350,
        Height = 140,
        WindowStartupLocation = WindowStartupLocation.CenterOwner
    };

    var panel = new StackPanel { Margin = new Avalonia.Thickness(15), Spacing = 10 };
    panel.Children.Add(new TextBlock { Text = "¿Desea guardar los cambios antes de salir?" });

    var btnGrid = new UniformGrid { Columns = 3 };

    var btnSi = new Button { Content = "Sí", HorizontalContentAlignment = Avalonia.Layout.HorizontalAlignment.Center, Margin = new Avalonia.Thickness(2) };
    var btnNo = new Button { Content = "No", HorizontalContentAlignment = Avalonia.Layout.HorizontalAlignment.Center, Margin = new Avalonia.Thickness(2) };
    var btnCancelar = new Button { Content = "Cancelar", HorizontalContentAlignment = Avalonia.Layout.HorizontalAlignment.Center, Margin = new Avalonia.Thickness(2) };

    btnSi.Click += async (_, _) =>
    {
        bool saved = await GuardarArchivoAsync();
        if (!saved) return; // el usuario canceló "Guardar como": no cerrar
        dialog.Close();
        _forceClose = true;
        Close();
    };

    btnNo.Click += (_, _) =>
    {
        dialog.Close();
        _forceClose = true;
        Close();
    };

    btnCancelar.Click += (_, _) => dialog.Close();

    btnGrid.Children.Add(btnSi);
    btnGrid.Children.Add(btnNo);
    btnGrid.Children.Add(btnCancelar);
    panel.Children.Add(btnGrid);
    dialog.Content = panel;

    await dialog.ShowDialog(this);
}
```

## Manejo de accesos directos por teclado

Se utiliza un manejador central de eventos `KeyDown` en `MainWindow` para capturar combinaciones de teclas con `Control` (o `Command` en macOS):

```csharp
private async void OnMainWindowKeyDown(object? sender, KeyEventArgs e)
{
    bool hasControl = e.KeyModifiers.HasFlag(KeyModifiers.Control) ||
                      e.KeyModifiers.HasFlag(KeyModifiers.Meta);

    if (!hasControl) return;

    var tb = EditorViewControl.GetTextBox();

    switch (e.Key)
    {
        case Key.N: e.Handled = true; NuevoArchivo(); break;
        case Key.O: e.Handled = true; await AbrirArchivoAsync(); break;
        case Key.S: e.Handled = true; await GuardarArchivoAsync(); break;
        case Key.Q: e.Handled = true; Close(); break;
        case Key.X: e.Handled = true; tb.Cut(); break;
        case Key.C: e.Handled = true; tb.Copy(); break;
        case Key.V: e.Handled = true; tb.Paste(); break;
        case Key.F: e.Handled = true; AbrirBusqueda(); break;
    }
}
```

<hr>

# 6. Pruebas funcionales y evidencia

Las pruebas funcionales permitieron verificar la correcta respuesta de la interfaz, el procesamiento de archivos, la personalización tipográfica y el control de cambios.

## 6.1 Evidencia de la interfaz principal

Muestra la ventana principal estructurada con `DockPanel`: menú superior, barra de herramientas con iconos cargados desde la ruta `avares://TextEditArZz/icons/`, el área de edición central y la barra de estado inferior.

![Figura 1. Interfaz principal del editor TextEditArZz](/editorarzz/main.png)

*Fuente: Elaboración propia.*

## 6.2 Prueba de captura de texto y métricas en tiempo real

Se ingresó texto en el área de edición para comprobar la actualización dinámica del contador de caracteres, la indicación de línea/columna y el estado `[Modificado]`.

![Figura 2. Edición de texto y actualización de la barra de estado](/editorarzz/pLinea.png)

*Fuente: Elaboración propia.*

## 6.3 Prueba de cambio de color de fondo

Se probó el menú **Formato → Color de fondo...** abriendo el diálogo `ColorPickerWindow`. Al seleccionar un color oscuro, el sistema adaptó automáticamente la fuente a color blanco.

![Figura 3. Selección de color de fondo y adaptación de contraste](/editorarzz/fColor.png)

*Fuente: Elaboración propia.*

## 6.4 Prueba de cambio de tipografía y tamaño

Se probó la ventana `FontPickerWindow` para modificar la fuente del documento a *Consolas* con tamaño de *18pt*, verificando la vista previa en tiempo real antes de aplicar los cambios.

![Figura 4. Personalización de fuente y tamaño](/editorarzz/pFont.png)

*Fuente: Elaboración propia.*

## 6.5 Prueba de búsqueda de texto

Se probó la ventana modal `SearchWindow` presionando `⌘F` / `Ctrl+F`. Al ingresar una palabra, el editor seleccionó y resaltó la posición del texto encontrado.

![Figura 5. Búsqueda de coincidencias dentro del editor](/editorarzz/tpicos.png)

*Fuente: Elaboración propia.*

## 6.6 Prueba de cuadro de diálogo "Acerca de..."

Se seleccionó la opción del menú **Ayuda → Acerca de...**, desplegando la ventana personalizada `AboutWindow` con la información del software.

![Figura 6. Ventana informativa Acerca de...](/editorarzz/about.png)

*Fuente: Elaboración propia.*

## 6.7 Prueba de confirmación al salir sin guardar

Al intentar cerrar la aplicación con modificaciones pendientes, la aplicación canceló el cierre automático y mostró la ventana modal pidiendo confirmación de guardado (Sí, No, Cancelar).

![Figura 7. Confirmación de guardado de cambios antes de salir](/editorarzz/ssave.png)

*Fuente: Elaboración propia.*

<hr>

# 7. Código fuente

A continuación se enlistan los archivos fuente clave del proyecto organizados según la arquitectura de componentes implementada.

## Estructura de módulos

```text
TextEditArZz/
├── MainWindow.axaml
├── MainWindow.axaml.cs
├── TextEditArZz.csproj
├── App.axaml
├── App.axaml.cs
├── Program.cs
├── models/
│   └── EditorState.cs
├── services/
│   └── FileService.cs
└── components/
    ├── MainMenuView/
    ├── ToolBarView/
    ├── EditorView/
    ├── StatusBarView/
    ├── AboutWindow/
    ├── SearchWindow/
    ├── ColorPickerWindow/
    └── FontPickerWindow/
```

## Resumen de responsabilidades

| Archivo / Componente | Responsabilidad |
| --- | --- |
| `MainWindow.axaml` | Define la disposición `DockPanel` que orquesta todos los subcomponentes. |
| `MainWindow.axaml.cs` | Conecta los eventos de menú/toolbar, intercepta atajos globales y gestiona el cierre. |
| `FileService.cs` | Ejecuta operaciones asíncronas de E/S mediante `StorageProvider`. |
| `EditorView` | Controla el `TextBox` principal, cálculo de cursor, fuente y contraste de color. |
| `MainMenuView` / `ToolBarView` | Exponen los eventos de acción y configuran los recursos de imagen de los botones. |
| `StatusBarView` | Actualiza la información de estado en la parte inferior de la ventana. |
| `ColorPickerWindow` / `FontPickerWindow` | Diálogos modales personalizados para formato de texto e interfaz. |
| `SearchWindow` | Captura patrones de texto y emite eventos de localización. |

<hr>

# 8. Conclusiones e informe técnico

## Dificultades encontradas y soluciones

1. **Equivalencia de Layouts:** En Windows Forms y Swing se utiliza tradicionalmente `Dock` o `BorderLayout`. Se resolvió usando `<DockPanel LastChildFill="True">` en Avalonia, acoplando los controles superiores e inferiores y dejando el editor en la región central rellenando el espacio.

2. **Estilo del enmarcado de foco (Focus Border):** El tema predeterminado Fluent mostraba un marco azul en el `TextBox` al escribir. Se solucionó mediante la anulación de las pseudo-clases `:focus` y `:pointerover` sobre el elemento `/template/ Border#PART_BorderElement` dentro de las reglas de estilo en `EditorView.axaml`.

3. **Contraste dinámico de texto:** Al cambiar el color de fondo a tonos oscuros (como negro o gris oscuro), el texto se volvía ilegible. Se implementó una fórmula de cálculo de luminancia relativa (con corrección gamma WCAG 2.1) en C# que cambia automáticamente el color del texto a blanco cuando la luminancia es menor a 0.5.

4. **Captura de atajos de teclado:** Avalonia requiere vincular los accesos directos al ciclo de eventos de la ventana contenedora. Se implementó un manejador `KeyDown` global en `MainWindow` que evalúa las banderas `KeyModifiers.Control` y `KeyModifiers.Meta`, marcando siempre `e.Handled = true` para evitar el procesamiento duplicado en el `TextBox`.

5. **Persistencia en sandbox de macOS:** El uso directo de `File.WriteAllTextAsync` fallaba en compilaciones empaquetadas por las restricciones de sandbox. Se migró a la API `IStorageFile.OpenWriteAsync()` provista por `StorageProvider`, unificando la E/S con el resto del servicio.

## Conclusión

El proyecto permitió construir un **Editor de Texto Simple** funcional, moderno y multiplataforma en **C# y Avalonia UI**. Se aplicó una arquitectura basada en **componentes reutilizables (`UserControl`)**, servicios desacoplados para el manejo de archivos asíncronos y una gestión de eventos eficiente.

El resultado final cumple con la totalidad de las especificaciones requeridas: manipulación de documentos de texto plano, atajos de teclado multiplataforma, barras de herramientas con iconos, diálogos de formato personalizados y confirmación de cambios al salir.
