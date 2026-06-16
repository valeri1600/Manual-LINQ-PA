# 2. Arquitectura 4 Capas

El modelo de 4 capas es una arquitectura muy utilizada en el desarrollo de software, ya que permite dividir una aplicación en partes con responsabilidades específicas. Esta separación facilita la organización del código, el mantenimiento del sistema y la reutilización de componentes. 

Ayuda a comprender el papel que cumple cada capa dentro del proyecto, permitiendo que cada una se encargue de una tarea determinada y mejorando la escalabilidad y el desarrollo de la aplicación.

<p align="center">
  <img src="./assets/imagenes/trans.png" alt="Concepto de LINQ y Capas" width="300">
</p>

## 1. Capa de Presentación
***¿Qué es?***

Es la capa que representa la parte visible del sistema. Es el medio por el cual el usuario interactúa con la aplicación mediante formularios, ventanas o páginas web.

Se utiliza para recibir la información que ingresa el usuario y mostrarle los resultados que genera el sistema. Su objetivo es facilitar la comunicación entre el usuario y la aplicación.

## Funciones
* Mostrar formularios y controles.
* Capturar los datos ingresados por el usuario.
* Mostrar mensajes y resultados.
* Enviar solicitudes a la capa de negocio.
* Recibir respuestas de la capa de negocio.

## 2. Capa de Negocio
***¿Qué es?***

Es la capa que contiene la lógica del sistema y las reglas que determinan cómo debe funcionar la aplicación. Se considera el cerebro del programa.

En esta capa se definen las reglas del sistema. Aquí se validan los datos recibidos, se realizan cálculos y se determina cómo debe comportarse la aplicación antes de interactuar con la base de datos.

## Funciones
* Aplicar reglas del negocio.
* Validar información.
* Realizar cálculos y procesos.
* Filtrar, ordenar o agrupar datos.
* Coordinar la comunicación con la capa de datos.
* Garantizar que se cumplan las políticas del sistema.


## 3. Capa de Datos
***¿Qué es?***

Es la capa encargada de la comunicación directa con la base de datos. Gestiona todas las operaciones relacionadas con el almacenamiento y recuperación de información.

Se utiliza para ejecutar consultas y modificaciones en la base de datos sin que las demás capas conozcan cómo se realizan internamente.

En la Capa de Datos se establece la conexión con SQL Server o mediante LINQ to SQL utilizando el DataContext.

### Funciones

* Conectarse a la base de datos.
* Ejecutar consultas mediante LINQ.
* Insertar registros (CRUD).
* Actualizar información (CRUD).
* Eliminar datos (CRUD).
* Recuperar información.
* Administrar conexiones y transacciones.

## 4. Capa de Entidades
***¿Qué es?***

Es la capa que contiene las clases que representan los objetos del sistema. Estas clases almacenan únicamente datos y sirven para transportar información entre las demás capas.

Se utiliza para definir la estructura de los datos que manejará la aplicación y facilitar el intercambio de información entre las capas.

## Funciones
* Representar entidades del sistema.
* Definir propiedades y atributos.
* Transportar datos entre capas.
* Mantener una estructura organizada de la información.

## Funcionamiento General del Sistema

Cuando el usuario realiza una acción en la interfaz, la información pasa por las diferentes capas del sistema de la siguiente manera:

* La Capa de Presentación recibe la acción del usuario.
* La Capa de Negocio valida y procesa la información.
* La Capa de Datos se encarga de almacenar o consultar la información en la base de datos.
* La respuesta retorna nuevamente a la interfaz para ser mostrada al usuario.

Este flujo garantiza una separación clara de responsabilidades y mejora la organización del sistema.

## Ejemplo práctico: Sistema de Dados con LINQ

Para demostrar el funcionamiento de LINQ dentro de una arquitectura de 4 capas, se desarrolló una aplicación en Visual Studio que simula lanzamientos de dos dados. El sistema genera resultados aleatorios, calcula puntajes y almacena la información en una base de datos SQL Server.

## Estructura del Proyecto
El sistema se divide en cuatro proyectos principales:

* **Capa de Entidades:** Biblioteca de clases (Class Library).
* **Capa de Datos:** Biblioteca de clases (Class Library).
* **Capa de Lógica de Negocio:** Biblioteca de clases (Class Library).
* **Capa de Presentación:** Aplicación Windows Forms.

Esta separación permite distribuir las responsabilidades del sistema, obteniendo un código más organizado, mantenible y escalable.

## Referencias entre Capas
Después de crear los proyectos, es necesario agregar las referencias entre ellos para permitir su comunicación:

* **Capa de Datos** → referencia a **Capa de Entidades**.
* **Capa de Lógica de Negocio** → referencia a **Capa de Entidades** y **Capa de Datos**.
* **Capa de Presentación** → referencia a **Capa de Entidades** y **Capa de Lógica de Negocio**.

<p align="center">
  <img src="./assets/imagenes/capas.png" alt="Concepto de LINQ y Capas" width="300">
</p>

## Conexión a la Base de Datos mediante LINQ to SQL

Para trabajar con LINQ to SQL, se necesita una base de datos creada en SQL Server. A partir de esta base de datos, Visual Studio genera una clase llamada DataContext, la cual actúa como puente entre la aplicación y la base de datos.

Esta clase permite acceder a las tablas como objetos del sistema, facilitando la ejecución de operaciones CRUD sin necesidad de escribir consultas SQL manualmente.

<p align="center">
  <img src="./assets/imagenes/SQL.png" alt="Concepto de LINQ y Capas" width="500">
</p>

La conexión con la base de datos se establece mediante la instancia del DataContext, utilizando el siguiente formato:

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    // Consultas LINQ
}
```
## Aplicación del modelo de 4 capas

**Capa de Entidades**

Se definió la clase `Dado_Entidad`, encargada de representar la información de cada lanzamiento:

* Identificador del lanzamiento.
* Valor del primer dado.
* Valor del segundo dado.
* Indicación de si ambos valores son iguales.
* Puntaje obtenido.
* Suma de los valores.
* Promedio del lanzamiento.

Esta entidad actúa como el medio de comunicación entre todas las capas.

<p align="center">
  <img src="./assets/imagenes/entidad.png" alt="Concepto de LINQ y Capas" width="300">
</p>

**Capa de Datos**

La capa de datos utiliza ADO.NET para insertar y recuperar información desde SQL Server mediante los métodos `InsertarDado()` y `ListarDados()`.

<p align="center">
  <img src="./assets/imagenes/datos1.png" alt="Concepto de LINQ y Capas" width="300">
</p>

Aunque el acceso a la base de datos se realiza mediante consultas SQL tradicionales, los datos recuperados son transformados en una lista de objetos `Dado_Entidad`, permitiendo posteriormente aplicar consultas LINQ.

<p align="center">
  <img src="./assets/imagenes/datos2.png" alt="Concepto de LINQ y Capas" width="300">
</p>

Más adelante se explicará cómo se implementan las consultas LINQ y las operaciones CRUD, las cuales permiten manipular la información de manera más eficiente dentro de la aplicación.

**Capa de Negocio**

En esta capa se aplican las reglas del sistema. Se valida que los valores de los dados estén entre 1 y 6, y se calculan la suma, el promedio y el puntaje.

Por ejemplo, antes de registrar un lanzamiento se verifica que los valores sean válidos:

<p align="center">
  <img src="./assets/imagenes/negocio1.png" alt="Concepto de LINQ y Capas" width="400">
</p>

Posteriormente, se calcula la suma y el promedio de los dados:

```csharp
dado.suma = dado1 + dado2;
dado.promedio = dado.suma / 2m;
```

Finalmente, se asigna el puntaje. Si ambos dados tienen el mismo valor, se establece `valoresIguales` como verdadero; cuando el valor repetido es 1 o 6 se asignan 5 puntos, mientras que para cualquier otro valor igual se asignan 3 puntos. Si los valores son diferentes, el puntaje es 0.

<p align="center">
  <img src="./assets/imagenes/negocio2.png" alt="Concepto de LINQ y Capas" width="300">
</p>

Una vez procesada la información, esta es enviada a la capa de datos para su almacenamiento mediante el método `InsertarDado()`.


**Capa de Presentación**

La capa de presentación es la encargada de la interacción entre el usuario y el sistema. En este proyecto, permite ingresar el número de lanzamientos de los dados, generar los resultados, mostrar la información almacenada en un `DataGridView` y representar gráficamente las estadísticas obtenidas mediante un `Chart`.

Por ejemplo, el usuario ingresa la cantidad de lanzamientos y, al presionar el botón correspondiente, el sistema genera valores aleatorios para los dados y actualiza automáticamente la tabla y el gráfico con los resultados obtenidos.

```csharp
int n = Convert.ToInt32(textBox_Lanzamientos.Text);

for (int i = 0; i < n; i++)
{
    Dado_Entidad dados = new Dado_Entidad();
    dados.valorDado1 = dado1.Next(1, 7);
    dados.valorDado2 = dado1.Next(1, 7);

    Capa_LogicaNegocio.Dado_Logica.InsertarDados(dados);
}

ActualizarDatos();
```

De esta manera, la capa de presentación se encarga de recibir las acciones del usuario y mostrar los resultados procesados por las demás capas.

<p align="center">
  <img src="./assets/imagenes/presentacion.png" alt="Concepto de LINQ y Capas" width="500">
</p>

### Conclusión del ejemplo

La arquitectura de 4 capas permite organizar un sistema de forma clara y estructurada, separando responsabilidades y mejorando el mantenimiento del código.

La integración de LINQ facilita el manejo de datos, permitiendo realizar consultas, filtros y análisis de manera más sencilla y eficiente dentro del sistema.


