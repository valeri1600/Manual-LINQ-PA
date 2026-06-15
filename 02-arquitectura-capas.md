# Arquitectura 4 Capas
<p align="center">
  <img src="./assets/imagenes/capas.png" alt="Concepto de LINQ y Capas" width="100">
</p>
## 1. Capa de Presentación
***¿Qué es?****

Es la capa que representa la parte visible del sistema. Es el medio por el cual el usuario interactúa con la aplicación mediante formularios, ventanas o páginas web.

Se utiliza para recibir la información que ingresa el usuario y mostrarle los resultados que genera el sistema. Su objetivo es facilitar la comunicación entre el usuario y la aplicación.

## Funciones
* Mostrar formularios y controles.
* Capturar los datos ingresados por el usuario.
* Mostrar mensajes y resultados.
* Enviar solicitudes a la capa de negocio.
* Recibir respuestas de la capa de negocio.
Mini ejemplo

En un sistema de inventario, el formulario "Registrar Producto" donde el usuario escribe el nombre, precio y stock del producto pertenece a esta capa.

## 2. Capa de Negocio
***¿Qué es?****

Es la capa que contiene la lógica del sistema y las reglas que determinan cómo debe funcionar la aplicación. Se considera el cerebro del programa.

Se utiliza para procesar la información recibida desde la interfaz, validar datos y tomar decisiones antes de enviarlos a la base de datos o devolver resultados al usuario.

## Funciones
* Aplicar reglas del negocio.
* Validar información.
* Realizar cálculos y procesos.
* Filtrar, ordenar o agrupar datos.
* Coordinar la comunicación con la capa de datos.
* Garantizar que se cumplan las políticas del sistema.


## 3. Capa de Datos
***¿Qué es?**

Es la capa encargada de la comunicación directa con la base de datos. Gestiona todas las operaciones relacionadas con el almacenamiento y recuperación de información.

Se utiliza para ejecutar consultas y modificaciones en la base de datos sin que las demás capas conozcan cómo se realizan internamente.

## Funciones
* Conectarse a la base de datos.
* Ejecutar consultas.
* Insertar registros.
* Actualizar información.
* Eliminar datos.
* Recuperar información.
* Administrar conexiones y transacciones.
Mini ejemplo

Cuando se guarda un nuevo cliente, esta capa ejecuta el comando que inserta los datos en SQL Server.

## 4. Capa de Entidades
***¿Qué es?***

Es la capa que contiene las clases que representan los objetos del sistema. Estas clases almacenan únicamente datos y sirven para transportar información entre las demás capas.

Se utiliza para definir la estructura de los datos que manejará la aplicación y facilitar el intercambio de información entre las capas.

## Funciones
* Representar entidades del sistema.
* Definir propiedades y atributos.
* Transportar datos entre capas.
* Mantener una estructura organizada de la información.
Mini ejemplo

La clase:

public class Producto
{
    public int IdProducto { get; set; }
    public string Nombre { get; set; }
    public decimal Precio { get; set; }
    public int Stock { get; set; }
}

representa la información de un producto y puede ser utilizada por todas las capas.

## Funcionamiento general

Cuando el usuario realiza una acción, la capa de presentación envía la solicitud a la capa de negocio. Esta procesa y valida la información, luego solicita a la capa de datos que acceda a la base de datos utilizando las entidades para transportar la información. Finalmente, la respuesta vuelve a la interfaz para mostrarse al usuario.

