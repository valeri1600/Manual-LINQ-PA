# Consultas Básicas con LINQ
Todos los ejemplos de esta sección se aplican al proyecto de simulación de dados utilizando **LINQ to SQL** en la **Capa de Datos**.

---

## Where — Filtrar registros

**¿Qué es?**

Permite obtener únicamente los elementos que cumplen una condición determinada.

**Ejemplo:**

Obtener los lanzamientos donde ambos dados tuvieron el mismo valor.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var iguales = contexto.Dados
                           .Where(d => d.valoresIguales)
                           .ToList();
}
```

---

## Select — Proyectar (seleccionar campos específicos)

**¿Qué es?**

Permite seleccionar únicamente la información que se necesita de cada registro.

**Ejemplo:**

Obtener solamente los puntajes registrados.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var puntajes = contexto.Dados
                           .Select(d => d.puntaje)
                           .ToList();
}
```

---

## OrderBy y OrderByDescending — Ordenar

**¿Qué es?**

Permiten ordenar la información de forma ascendente o descendente.

**Ejemplo:**

Obtener los lanzamientos ordenados de mayor a menor puntaje.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var ordenados = contexto.Dados
                            .OrderByDescending(d => d.puntaje)
                            .ToList();
}
```

---

## FirstOrDefault — Obtener un solo registro

**¿Qué es?**

Devuelve el primer elemento que cumple una condición. Si no existe, retorna `null`.

**Ejemplo:**

Obtener el primer lanzamiento con puntaje de 5.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var lanzamiento = contexto.Dados
                              .FirstOrDefault(d => d.puntaje == 5);
}
```

> **Importante:** Antes de utilizar el resultado, se debe verificar que no sea `null`.

```csharp
if (lanzamiento == null)
{
    MessageBox.Show("No existen lanzamientos con puntaje 5.");
}
```

---

## Any — Verificar si existe algún registro

**¿Qué es?**

Permite comprobar si existe al menos un elemento que cumpla una condición.

**Ejemplo:**

Verificar si existe algún lanzamiento con dados iguales.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    bool existe = contexto.Dados
                           .Any(d => d.valoresIguales);
}
```

---

## Take — Obtener una cantidad específica de registros

**¿Qué es?**

Permite seleccionar un número determinado de elementos.

**Ejemplo:**

Obtener los cinco primeros lanzamientos registrados.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var primerosCinco = contexto.Dados
                                .Take(5)
                                .ToList();
}
```

---

## Distinct — Eliminar elementos repetidos

**¿Qué es?**

Permite obtener únicamente valores únicos, eliminando duplicados.

**Ejemplo:**

Obtener los puntajes diferentes obtenidos durante los lanzamientos.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var puntajesUnicos = contexto.Dados
                                 .Select(d => d.puntaje)
                                 .Distinct()
                                 .ToList();
}
```

---

## Combinando operadores

**¿Qué es?**

LINQ permite combinar varios operadores para obtener resultados más específicos.

**Ejemplo:**

Obtener los cinco lanzamientos con puntaje mayor a cero, ordenados de mayor a menor.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var mejores = contexto.Dados
                           .Where(d => d.puntaje > 0)
                           .OrderByDescending(d => d.puntaje)
                           .Take(5)
                           .ToList();
}
```

---

