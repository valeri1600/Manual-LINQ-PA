# 4. Funciones de Agregado en LINQ

Las funciones de agregado en LINQ permiten realizar **cálculos sobre conjuntos de datos**: sumar, contar, obtener promedios, valores máximos o mínimos. Se aplican sobre colecciones y devuelven **un único resultado**.

---

## Estructura base en la Capa de Datos

Todos los ejemplos de esta sección usan `DadosDataContext`, que es el contexto LINQ to SQL generado a partir de la base de datos SQL Server.

```csharp

using (DadosDataContext contexto = new DadosDataContext())
{
    var resultado = contexto.Dados
                            .OperadorLINQ(...)
                            .ToList();
}
```

---

## Sintaxis de Métodos

### Count — Contar registros

Permite contar la cantidad de elementos en una colección.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    int total = contexto.Dados.Count();
}
```

**Con condición:**
Contar solo los lanzamientos que obtuvieron puntaje

```csharp
int total = contexto.Dados.Count(d => d.puntaje > 0);
```

---

### Sum — Sumar valores

Suma los valores de una propiedad numérica.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    int sumaTotal = contexto.Dados.Sum(d => d.puntaje);
}
```

---

### Average — Promedio

Calcula el promedio de los valores.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    double promedio = contexto.Dados.Average(d => d.puntaje);
}
```

---

### Max — Valor máximo

Obtiene el valor más alto de una columna.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    int maximo = contexto.Dados.Max(d => d.puntaje);
}
```

---

### Min — Valor mínimo

Obtiene el valor más bajo de una columna.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    int minimo = contexto.Dados.Min(d => d.puntaje);
}
```

---

## Funciones de Agregado con condición

Ejemplo combinado: obtener estadísticas **solo de los lanzamientos con puntaje mayor a 0**.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    int total = contexto.Dados.Count(d => d.puntaje > 0);

    int suma = contexto.Dados
                       .Where(d => d.puntaje > 0)
                       .Sum(d => d.puntaje);

    double promedio = contexto.Dados
                              .Where(d => d.puntaje > 0)
                              .Average(d => d.puntaje);

    int maximo = contexto.Dados
                         .Where(d => d.puntaje > 0)
                         .Max(d => d.puntaje);

    int minimo = contexto.Dados
                         .Where(d => d.puntaje > 0)
                         .Min(d => d.puntaje);
}
```

---

## Sintaxis de Consulta

En sintaxis de consulta (estilo SQL), las funciones de agregado se encadenan **después** de la expresión `select`.

### Count

```csharp
int total =
    (from d in contexto.Dados
     select d).Count();
```

**Con condición:**
```csharp
int total =
    (from d in contexto.Dados
     where d.puntaje > 0
     select d).Count();
```

### Sum

```csharp
int suma =
    (from d in contexto.Dados
     select d.puntaje).Sum();
```

### Average

```csharp
double promedio =
    (from d in contexto.Dados
     select d.puntaje).Average();
```

### Max

```csharp
int maximo =
    (from d in contexto.Dados
     select d.puntaje).Max();
```

### Min

```csharp
int minimo =
    (from d in contexto.Dados
     select d.puntaje).Min();
```

---

## Equivalencia SQL ↔ LINQ

Una de las ventajas de LINQ es que reproduce exactamente lo que haría SQL, pero en C#.

**SQL equivalente:**
```sql
SELECT
    COUNT(*)      AS Total,
    SUM(puntaje)  AS Suma,
    AVG(puntaje)  AS Promedio,
    MAX(puntaje)  AS Maximo,
    MIN(puntaje)  AS Minimo
FROM Dados
WHERE puntaje > 0;
```

**LINQ equivalente:**
```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var datos = contexto.Dados.Where(d => d.puntaje > 0);

    int    total    = datos.Count();
    int    suma     = datos.Sum(d => d.puntaje);
    double promedio = datos.Average(d => d.puntaje);
    int    maximo   = datos.Max(d => d.puntaje);
    int    minimo   = datos.Min(d => d.puntaje);
}
```


---

## Tabla de referencia rápida

| Función | ¿Qué hace? | Retorna | SQL equivalente |
|---------|-----------|---------|-----------------|
| `.Count()` | Cuenta elementos | `int` | `COUNT(*)` |
| `.Count(condición)` | Cuenta con filtro | `int` | `COUNT(*) WHERE` |
| `.Sum(x => x.Campo)` | Suma numérica | `int` / `double` | `SUM()` |
| `.Average(x => x.Campo)` | Promedio | `double` | `AVG()` |
| `.Max(x => x.Campo)` | Valor más alto | tipo del campo | `MAX()` |
| `.Min(x => x.Campo)` | Valor más bajo | tipo del campo | `MIN()` |

---


