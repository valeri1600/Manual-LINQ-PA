# 3. Consultas Básicas con LINQ

Todos los ejemplos de esta sección están en la **Capa de Datos** (LINQ to Entities) o **Capa de Negocio** (LINQ to Objects), según se indica.

---

## Where — Filtrar registros

**Capa:** Datos (LINQ to Entities)

```csharp
// Obtener tutorías activas de un estudiante específico
// Ref: Datos/RepositorioTutoria.cs → método ObtenerActivasPorEstudiante()
public List<Tutoria> ObtenerActivasPorEstudiante(int estudianteId)
{
    using (var context = new ContextoBD())
    {
        return context.Tutorias
                      .Where(t => t.EstudianteId == estudianteId
                               && t.Activa == true)
                      .ToList();
    }
}
```

**SQL generado por Entity Framework:**
```sql
SELECT * FROM Tutorias
WHERE EstudianteId = @p0 AND Activa = 1
```

---

## Select — Proyectar (seleccionar solo algunos campos)

**Capa:** Negocio (LINQ to Objects)

```csharp
// Obtener solo los nombres de estudiantes con tutoría activa
// Ref: Negocio/NegocioTutoria.cs → método ObtenerNombresActivos()
public List<string> ObtenerNombresActivos()
{
    var tutorias = _repositorio.ObtenerTodas();

    return tutorias
           .Where(t => t.Activa)
           .Select(t => t.Estudiante.Nombre)
           .Distinct()
           .ToList();
}
```

---

## OrderBy y OrderByDescending — Ordenar

**Capa:** Datos (LINQ to Entities)

```csharp
// Obtener pacientes ordenados por apellido, luego por nombre
// Ref: Datos/RepositorioPaciente.cs → método ObtenerOrdenados()
public List<Paciente> ObtenerOrdenados()
{
    using (var context = new ContextoBD())
    {
        return context.Pacientes
                      .OrderBy(p => p.Apellido)
                      .ThenBy(p => p.Nombre)
                      .ToList();
    }
}
```

---

## FirstOrDefault — Obtener un solo registro

**Capa:** Datos (LINQ to Entities)

```csharp
// Buscar un paciente por su cédula
// Ref: Datos/RepositorioPaciente.cs → método BuscarPorCedula()
public Paciente BuscarPorCedula(string cedula)
{
    using (var context = new ContextoBD())
    {
        // Retorna el paciente o null si no existe
        return context.Pacientes
                      .FirstOrDefault(p => p.Cedula == cedula);
    }
}
```

> ⚠️ **Importante:** Siempre verifica que el resultado no sea `null` antes de usarlo en la capa de Negocio:
```csharp
var paciente = _repositorio.BuscarPorCedula(cedula);
if (paciente == null)
    throw new Exception("Paciente no encontrado.");
```

---

## Include — Cargar datos relacionados (JOIN)

**Capa:** Datos (LINQ to Entities)

```csharp
// Obtener tutorías incluyendo los datos del estudiante y del tutor
// Ref: Datos/RepositorioTutoria.cs → método ObtenerConRelaciones()
public List<Tutoria> ObtenerConRelaciones()
{
    using (var context = new ContextoBD())
    {
        return context.Tutorias
                      .Include(t => t.Estudiante)
                      .Include(t => t.Tutor)
                      .Where(t => t.Activa)
                      .ToList();
    }
}
```

**Equivalente en SQL:**
```sql
SELECT t.*, e.*, tu.*
FROM Tutorias t
INNER JOIN Estudiantes e ON t.EstudianteId = e.Id
INNER JOIN Tutores tu ON t.TutorId = tu.Id
WHERE t.Activa = 1
```

---

## Any — Verificar si existe algo

**Capa:** Negocio (LINQ to Objects)

```csharp
// Verificar si un estudiante ya tiene una tutoría registrada hoy
// Ref: Negocio/NegocioTutoria.cs → método YaTieneTutoriaHoy()
public bool YaTieneTutoriaHoy(int estudianteId)
{
    var tutorias = _repositorio.ObtenerPorEstudiante(estudianteId);

    return tutorias.Any(t => t.Fecha.Date == DateTime.Today);
}
```

---

## Combinando operadores

**Capa:** Datos (LINQ to Entities)

```csharp
// Obtener las 5 tutorías más recientes de un estudiante
// Ref: Datos/RepositorioTutoria.cs → método ObtenerUltimas()
public List<Tutoria> ObtenerUltimas(int estudianteId, int cantidad = 5)
{
    using (var context = new ContextoBD())
    {
        return context.Tutorias
                      .Where(t => t.EstudianteId == estudianteId)
                      .OrderByDescending(t => t.Fecha)
                      .Take(cantidad)
                      .Include(t => t.Estudiante)
                      .ToList();
    }
}
```

---

[← Arquitectura de Capas](02-arquitectura-capas.md) | [Siguiente: Funciones de Agregado →](04-funciones-agregado.md)
