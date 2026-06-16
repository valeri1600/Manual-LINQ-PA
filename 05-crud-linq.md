# 5. Operaciones CRUD con LINQ to SQL

## ¿Qué es CRUD?

CRUD es el conjunto de operaciones fundamentales que permiten manipular la información almacenada en una base de datos:

* **Create (Crear):** insertar nuevos registros.
* **Read (Leer):** consultar o recuperar información.
* **Update (Actualizar):** modificar registros existentes.
* **Delete (Eliminar):** eliminar registros almacenados.

En LINQ to SQL, estas operaciones se realizan mediante el `DataContext`, el cual se encarga de gestionar la comunicación entre la aplicación y la base de datos.

---

## Create (Insertar)

Permite agregar un nuevo registro a la tabla.

### Sintaxis de métodos (Lambda)

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    Dados dado = new Dados
    {
        valorDado1 = 4,
        valorDado2 = 4,
        valoresIguales = true,
        puntaje = 3,
        suma = 8,
        promedio = 4
    };

    contexto.Dados.InsertOnSubmit(dado);
    contexto.SubmitChanges();
}
```

### Explicación

* `InsertOnSubmit()` registra el objeto que se desea insertar.
* `SubmitChanges()` ejecuta los cambios en la base de datos.

---

## Read (Consultar)

Permite recuperar información almacenada.

### Sintaxis de métodos (Lambda)

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var resultado = contexto.Dados
                            .Where(d => d.puntaje > 0)
                            .ToList();
}
```

### Sintaxis de consulta

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var resultado =
        (from d in contexto.Dados
         where d.puntaje > 0
         select d)
        .ToList();
}
```

### Explicación

* `Where()` o `where` permiten filtrar registros.
* `ToList()` ejecuta la consulta y devuelve una lista.

---

## Update (Actualizar)

Permite modificar información existente.

### Sintaxis de métodos (Lambda)

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var dado = contexto.Dados
                       .FirstOrDefault(d => d.id == 1);

    if (dado != null)
    {
        dado.puntaje = 5;
        dado.promedio = 5;

        contexto.SubmitChanges();
    }
}
```

### Sintaxis de consulta

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var dado =
        (from d in contexto.Dados
         where d.id == 1
         select d)
        .FirstOrDefault();

    if (dado != null)
    {
        dado.puntaje = 5;
        dado.promedio = 5;

        contexto.SubmitChanges();
    }
}
```

### Explicación

* Primero se obtiene el registro que se desea modificar.
* Se actualizan sus propiedades.
* `SubmitChanges()` guarda los cambios realizados.

---

## Delete (Eliminar)

Permite eliminar registros de la base de datos.

### Sintaxis de métodos (Lambda)

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var dado = contexto.Dados
                       .FirstOrDefault(d => d.id == 1);

    if (dado != null)
    {
        contexto.Dados.DeleteOnSubmit(dado);
        contexto.SubmitChanges();
    }
}
```

### Sintaxis de consulta

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var dado =
        (from d in contexto.Dados
         where d.id == 1
         select d)
        .FirstOrDefault();

    if (dado != null)
    {
        contexto.Dados.DeleteOnSubmit(dado);
        contexto.SubmitChanges();
    }
}
```

### Explicación

* Se localiza el registro que será eliminado.
* `DeleteOnSubmit()` marca el objeto para su eliminación.
* `SubmitChanges()` ejecuta definitivamente el cambio en la base de datos.

---

## Resumen

| Operación         | Método principal                          | Acción                           |
| ----------------- | ----------------------------------------- | -------------------------------- |
| Create            | `InsertOnSubmit()`                        | Inserta un nuevo registro        |
| Read              | `Where()`, `select`                       | Consulta información             |
| Update            | Modificar propiedades + `SubmitChanges()` | Actualiza registros              |
| Delete            | `DeleteOnSubmit()`                        | Elimina registros                |
| Confirmar cambios | `SubmitChanges()`                         | Guarda los cambios en SQL Server |


## Ejemplo práctico de CRUD con LINQ to SQL

En la **Capa de Datos** del proyecto de simulación de dados se implementan las operaciones CRUD, las cuales permiten crear, consultar, actualizar y eliminar información almacenada en la base de datos. Esta capa es la única que tiene acceso directo a SQL Server, por lo que se encarga de gestionar la persistencia de los datos mediante LINQ to SQL.

## Método Create — Insertar

Este método permite registrar un nuevo lanzamiento en la tabla `Dados`. Recibe un objeto `Dado_Entidad` desde la Capa de Negocio, transfiere sus datos a la entidad `Dados` generada por LINQ to SQL y almacena la información en la base de datos. Una vez realizada la inserción, el identificador generado automáticamente es asignado nuevamente a la entidad antes de retornarla.

```csharp
public static Dado_Entidad InsertarDado(Dado_Entidad dado)
{
    Dados dadoLinQ = new Dados();

    dadoLinQ.valorDado1 = dado.valorDado1;
    dadoLinQ.valorDado2 = dado.valorDado2;
    dadoLinQ.valoresIguales = dado.valoresIguales;
    dadoLinQ.puntaje = dado.puntaje;
    dadoLinQ.suma = dado.suma;
    dadoLinQ.promedio = dado.promedio;

    using (DadoDataContext contexto = new DadoDataContext())
    {
        contexto.Dados.InsertOnSubmit(dadoLinQ);
        contexto.SubmitChanges();
    }

    dado.id = dadoLinQ.id;

    return dado;
}
```

### Aspectos importantes

* `dado` es un objeto de tipo `Dado_Entidad`, perteneciente a la Capa de Entidades.
* `dadoLinQ` representa la entidad `Dados` generada por LINQ to SQL y asociada a la tabla real de la base de datos.
* Los datos de `Dado_Entidad` son transferidos a `Dados` antes de realizar la inserción.
* `InsertOnSubmit()` registra el objeto para ser insertado.
* `SubmitChanges()` ejecuta la operación y guarda el registro en SQL Server.
* El `id` generado automáticamente por la base de datos es asignado nuevamente al objeto `Dado_Entidad`.
* Finalmente, se devuelve la entidad con toda su información actualizada.



## Método Update — Actualizar

Este método permite modificar la información de un lanzamiento previamente registrado. Para ello, se busca el registro correspondiente mediante su `id`, se actualizan sus propiedades con los nuevos valores recibidos desde `Dado_Entidad` y, finalmente, se guardan los cambios en la base de datos.

```csharp
public static Dado_Entidad Actualizar(Dado_Entidad dado)
{
    using (DadoDataContext contexto = new DadoDataContext())
    {
        Dados dadoLinq = contexto.Dados
                                 .FirstOrDefault(d => d.id == dado.id);

        dadoLinq.id = dado.id;
        dadoLinq.valorDado1 = dado.valorDado1;
        dadoLinq.valorDado2 = dado.valorDado2;
        dadoLinq.puntaje = dado.puntaje;
        dadoLinq.suma = dado.suma;
        dadoLinq.promedio = dado.promedio;

        contexto.SubmitChanges();

        return dado;
    }
}
```

### Aspectos importantes

* `FirstOrDefault()` permite localizar el registro que se desea modificar.
* `SubmitChanges()` guarda definitivamente los cambios realizados en SQL Server.
* Se devuelve la entidad actualizada.

---

## Método Delete — Eliminar

Este método permite eliminar un lanzamiento de la base de datos. Para ello, se localiza el registro mediante su identificador y posteriormente se marca para su eliminación.

```csharp
public static bool Eliminar(Dado_Entidad dado)
{
    using (DadoDataContext contexto = new DadoDataContext())
    {
        Dados dadoLinQ = contexto.Dados
                                 .FirstOrDefault(d => d.id == dado.id);

        contexto.Dados.DeleteOnSubmit(dadoLinQ);
        contexto.SubmitChanges();

        return true;
    }
}
```

### Aspectos importantes

* `FirstOrDefault()` obtiene el registro que será eliminado.
* `DeleteOnSubmit()` marca el objeto para su eliminación.
* `SubmitChanges()` ejecuta la eliminación en la base de datos.
* El método devuelve `true` cuando la operación finaliza correctamente.

---

## Método Read — Leer o Consultar

Este método permite recuperar todos los lanzamientos almacenados en la tabla `Dados`. Los registros obtenidos desde LINQ to SQL son convertidos a objetos `Dado_Entidad` para ser utilizados por las demás capas de la aplicación.

```csharp
public static List<Dado_Entidad> Leer()
{
    List<Dado_Entidad> listaDado = new List<Dado_Entidad>();
    List<Dados> listaLinQ = new List<Dados>();

    using (DadoDataContext contexto = new DadoDataContext())
    {
        listaLinQ = contexto.Dados.ToList();
    }

    foreach (var item in listaLinQ)
    {
        listaDado.Add(new Dado_Entidad
        {
            id = item.id,
            valorDado1 = item.valorDado1,
            valorDado2 = item.valorDado2,
            valoresIguales = item.valoresIguales,
            puntaje = item.puntaje,
            suma = item.suma,
            promedio = item.promedio
        });
    }

    return listaDado;
}
```

### Aspectos importantes

* `ToList()` ejecuta la consulta y obtiene todos los registros de la tabla.
* Los objetos `Dados` son transformados a `Dado_Entidad`.
* El método devuelve una lista de entidades que podrá ser utilizada por la Capa de Negocio y la Capa de Presentación.

