Esta sección presenta escenarios reales resueltos con LINQ, tomados directamente del **Sistema de Gestión de Pacientes** desarrollado en clase. Se sigue el flujo: **problema → código por capa → resultado**.

> **Proyecto fuente:** `GestionUsuarios` — Sistema de registro y administración de pacientes con géneros, afiliación al IESS y gestión completa CRUD.

---

## Estructura del proyecto

```
GestionUsuarios/
├── GestionUsuarios_Entidades/     → PacienteEntidades.cs, GeneroEntidades.cs
├── GestionUsuariosLinQ/           → PacienteDatos.cs, GeneroDatos.cs
├── GestionUsuarios_LogicaNegocio/ → PacienteNegocio.cs, GeneroNegocio.cs
└── Presentacion/                  → Form_Paciente.cs
```

**Contexto LINQ to SQL utilizado:** `ModePacienteDataContext`  
**Tablas:** `Paciente`, `Genero`

---

## Caso 1: Listar todos los pacientes con su género

### Problema
El formulario necesita mostrar en un `DataGridView` todos los pacientes registrados, incluyendo el **nombre del género** (no solo el ID), que viene de una tabla separada.

### Entidades involucradas

```csharp
// Ref: GestionUsuarios_Entidades/PacienteEntidades.cs
public class PacienteEntidades
{
    public int      Id              { get; set; }
    public int      Id_Genero       { get; set; }
    public string   Genero          { get; set; }   // nombre del género (no el ID)
    public string   Nombre          { get; set; }
    public string   Apellido        { get; set; }
    public string   Cedula          { get; set; }
    public DateTime FechaNacimiento { get; set; }
    public string   Telefono        { get; set; }
    public string   Direccion       { get; set; }
    public bool     Afiliado        { get; set; }
    public string   CodigoIESS      { get; set; }
}
```

### Paso 1 — Capa de Datos: consulta LINQ to SQL

```csharp
// Ref: GestionUsuariosLinQ/PacienteDatos.cs → método DevolverListaPaciente()
public static List<PacienteEntidades> DevolverListaPaciente()
{
    List<PacienteEntidades> listaPaciente    = new List<PacienteEntidades>();
    List<Paciente>          listaPacienteLinq = new List<Paciente>();

    using (ModePacienteDataContext contexto = new ModePacienteDataContext())
    {
        // LINQ to SQL: trae todos los pacientes de la base de datos
        var resultado = from p in contexto.Paciente
                        select p;

        listaPacienteLinq = resultado.ToList();
    }

    // Convierte cada objeto LINQ en una entidad del sistema
    // y resuelve el nombre del género consultando GeneroDatos
    foreach (var item in listaPacienteLinq)
    {
        listaPaciente.Add(new PacienteEntidades(
            item.id,
            (int)item.id_Genero,
            item.nombre,
            GeneroDatos.DevolverNombreGeneroPorId((int)item.id_Genero), // JOIN manual
            item.apellido,
            item.cedula,
            Convert.ToDateTime(item.fechaNacimiento),
            item.direccion,
            item.telefono,
            (bool)item.afiliado,
            item.codigoIess
        ));
    }

    return listaPaciente;
}
```

> 💡 **Observación:** Como el proyecto usa LINQ to SQL (no Entity Framework), el "JOIN" entre `Paciente` y `Genero` se resuelve manualmente llamando a `GeneroDatos.DevolverNombreGeneroPorId()` por cada paciente dentro del `foreach`.

### Paso 2 — Capa de Negocio: expone el método

```csharp
// Ref: GestionUsuarios_LogicaNegocio/PacienteNegocio.cs → método DevolverListaPaciente()
public static List<PacienteEntidades> DevolverListaPaciente()
{
    return PacienteDatos.DevolverListaPaciente();
}
```

### Paso 3 — Capa de Presentación: llena el DataGridView

```csharp
// Ref: Presentacion/Form_Paciente.cs → método para cargar la grilla
private void CargarListaPacientes()
{
    dataGridView1.DataSource = PacienteNegocio.DevolverListaPaciente();
}
```

### Resultado esperado

| Id | Genero    | Nombre  | Apellido | Cédula      | Afiliado | Código IESS |
|----|-----------|---------|----------|-------------|----------|-------------|
| 1  | Masculino | Carlos  | Mora     | 1801234567  | Sí       | A001        |
| 2  | Femenino  | Ana     | García   | 1809876543  | No       |             |
| 3  | Femenino  | Diana   | Salazar  | 1805551234  | Sí       | A003        |

---

## Caso 2: Buscar un paciente por ID

### Problema
Al seleccionar un paciente en la grilla, el formulario debe cargar todos sus datos en los campos de texto para poder editarlos.

### Solución LINQ

**Capa de Datos — `FirstOrDefault` con condición:**

```csharp
// Ref: GestionUsuariosLinQ/PacienteDatos.cs → método CargarPacientePorId()
public static PacienteEntidades CargarPacientePorId(int id)
{
    PacienteEntidades paciente = new PacienteEntidades();

    using (ModePacienteDataContext contexto = new ModePacienteDataContext())
    {
        // LINQ: busca el primer paciente cuyo id coincida, o null si no existe
        Paciente pacienteLinQ = contexto.Paciente.FirstOrDefault(p => p.id == id);

        // Mapea el objeto LINQ a la entidad del sistema
        paciente.Id              = pacienteLinQ.id;
        paciente.Id_Genero       = (int)pacienteLinQ.id_Genero;
        paciente.Nombre          = pacienteLinQ.nombre;
        paciente.Apellido        = pacienteLinQ.apellido;
        paciente.Cedula          = pacienteLinQ.cedula;
        paciente.FechaNacimiento = Convert.ToDateTime(pacienteLinQ.fechaNacimiento);
        paciente.Telefono        = pacienteLinQ.telefono;
        paciente.Direccion       = pacienteLinQ.direccion;
        paciente.Afiliado        = (bool)pacienteLinQ.afiliado;
        paciente.CodigoIESS      = pacienteLinQ.codigoIess;

        return paciente;
    }
}
```

**Capa de Negocio:**

```csharp
// Ref: GestionUsuarios_LogicaNegocio/PacienteNegocio.cs → método CargarPacientePorId()
public static PacienteEntidades CargarPacientePorId(int id)
{
    return PacienteDatos.CargarPacientePorID(id);
}
```

> ⚠️ `FirstOrDefault` devuelve `null` si no encuentra el registro. Si el ID no existe en la BD, el acceso a `pacienteLinQ.nombre` lanzaría una excepción `NullReferenceException`. En producción siempre se verifica antes de mapear.

---

## Caso 3: Registrar un nuevo paciente con validación

### Problema
Antes de insertar un paciente en la base de datos, el sistema debe **validar** que el nombre no esté vacío y luego persistir los datos usando una transacción.

### Solución — Flujo completo por capas

**Capa de Negocio — validación + transacción:**

```csharp
// Ref: GestionUsuarios_LogicaNegocio/PacienteNegocio.cs → método GuardarPaciente()
public static PacienteEntidades GuardarPaciente(PacienteEntidades paciente)
{
    try
    {
        using (TransactionScope scope = new TransactionScope())
        {
            // Regla de negocio: el nombre es obligatorio
            if (string.IsNullOrWhiteSpace(paciente.Nombre))
            {
                paciente.errror = "El nombre es obligatorio";
                return paciente;
            }

            // Si pasa la validación, llama a la capa de Datos
            PacienteDatos.Nuevo(paciente);
            scope.Complete();       // Confirma la transacción
            paciente.errror = "";
        }
    }
    catch (Exception ex)
    {
        paciente.errror = ex.Message;   // Si algo falla, captura el error
    }

    return paciente;
}
```

**Capa de Datos — `InsertOnSubmit` con LINQ to SQL:**

```csharp
// Ref: GestionUsuariosLinQ/PacienteDatos.cs → método Nuevo()
public static PacienteEntidades Nuevo(PacienteEntidades paciente)
{
    // Crea el objeto LINQ que mapea a la tabla Paciente en SQL
    Paciente pacienteLinq = new Paciente();
    pacienteLinq.id              = paciente.Id;
    pacienteLinq.id_Genero       = (int)paciente.Id_Genero;
    pacienteLinq.nombre          = paciente.Nombre;
    pacienteLinq.apellido        = paciente.Apellido;
    pacienteLinq.cedula          = paciente.Cedula;
    pacienteLinq.fechaNacimiento = paciente.FechaNacimiento;
    pacienteLinq.telefono        = paciente.Telefono;
    pacienteLinq.direccion       = paciente.Direccion;
    pacienteLinq.afiliado        = paciente.Afiliado;
    pacienteLinq.codigoIess      = paciente.CodigoIESS;

    using (ModePacienteDataContext contexto = new ModePacienteDataContext())
    {
        contexto.Paciente.InsertOnSubmit(pacienteLinq);   // Marca para insertar
        contexto.SubmitChanges();                          // Ejecuta INSERT en SQL
    }

    paciente.Id = pacienteLinq.id;   // Recupera el ID generado por la BD
    return paciente;
}
```

**SQL generado por LINQ to SQL:**
```sql
INSERT INTO Paciente (id_Genero, nombre, apellido, cedula, fechaNacimiento,
                      telefono, direccion, afiliado, codigoIess)
VALUES (@p0, @p1, @p2, @p3, @p4, @p5, @p6, @p7, @p8)
```

---

## Caso 4: Actualizar datos de un paciente

### Problema
El usuario modifica los datos de un paciente en el formulario y presiona "Guardar". El sistema debe actualizar **solo ese registro** en la base de datos.

### Solución LINQ

```csharp
// Ref: GestionUsuariosLinQ/PacienteDatos.cs → método Actualizar()
public static PacienteEntidades Actualizar(PacienteEntidades paciente)
{
    using (ModePacienteDataContext contexto = new ModePacienteDataContext())
    {
        // LINQ: localiza el registro existente por su ID
        Paciente pacienteLinQ = contexto.Paciente.FirstOrDefault(p => p.id == paciente.Id);

        // Sobreescribe los campos con los nuevos valores
        pacienteLinQ.id_Genero       = (int)paciente.Id_Genero;
        pacienteLinQ.nombre          = paciente.Nombre;
        pacienteLinQ.apellido        = paciente.Apellido;
        pacienteLinQ.cedula          = paciente.Cedula;
        pacienteLinQ.fechaNacimiento = paciente.FechaNacimiento;
        pacienteLinQ.telefono        = paciente.Telefono;
        pacienteLinQ.direccion       = paciente.Direccion;
        pacienteLinQ.afiliado        = paciente.Afiliado;
        pacienteLinQ.codigoIess      = paciente.CodigoIESS;

        // SubmitChanges detecta los cambios y genera el UPDATE automáticamente
        contexto.SubmitChanges();
        return paciente;
    }
}
```

**SQL generado:**
```sql
UPDATE Paciente
SET id_Genero = @p0, nombre = @p1, apellido = @p2, cedula = @p3,
    fechaNacimiento = @p4, telefono = @p5, direccion = @p6,
    afiliado = @p7, codigoIess = @p8
WHERE id = @p9
```

---

## Caso 5: Eliminar un paciente

### Problema
El usuario selecciona un paciente y presiona "Eliminar". El sistema debe borrarlo de la base de datos.

### Solución LINQ

```csharp
// Ref: GestionUsuariosLinQ/PacienteDatos.cs → método EliminarPacientePorID()
public static bool EliminarPacientePorID(int id)
{
    try
    {
        using (ModePacienteDataContext contexto = new ModePacienteDataContext())
        {
            // LINQ: encuentra el registro a eliminar
            Paciente pacienteLinQ = contexto.Paciente.FirstOrDefault(p => p.id == id);

            contexto.Paciente.DeleteOnSubmit(pacienteLinQ);   // Marca para eliminar
            contexto.SubmitChanges();                          // Ejecuta DELETE en SQL
            return true;
        }
    }
    catch (Exception)
    {
        return false;
    }
}
```

**Capa de Negocio:**
```csharp
// Ref: GestionUsuarios_LogicaNegocio/PacienteNegocio.cs → método EliminarPacientePorID()
public static bool EliminarPacientePorID(int id)
{
    return PacienteDatos.Eliminar(id);
}
```

---

## Caso 6: Cargar géneros en un ComboBox

### Problema
Al abrir el formulario, el `ComboBox` de género debe llenarse con todos los géneros de la base de datos.

### Solución LINQ

**Capa de Datos:**
```csharp
// Ref: GestionUsuariosLinQ/GeneroDatos.cs → método DevolverListaGenero()
public static List<GeneroEntidades> DevolverListaGenero()
{
    List<GeneroEntidades> listaGeneroEntidades = new List<GeneroEntidades>();
    List<Genero>          listaGenero          = new List<Genero>();

    using (ModePacienteDataContext contexto = new ModePacienteDataContext())
    {
        // LINQ to SQL: selecciona todos los géneros
        var resultado = from g in contexto.Genero
                        select g;

        listaGenero = resultado.ToList();
    }

    foreach (var item in listaGenero)
    {
        listaGeneroEntidades.Add(new GeneroEntidades(item.id, item.nombre));
    }

    return listaGeneroEntidades;
}
```

**Capa de Negocio:**
```csharp
// Ref: GestionUsuarios_LogicaNegocio/GeneroNegocio.cs → método DevolverListasGeneros()
public static List<GeneroEntidades> DevolverListasGeneros()
{
    return GeneroDatos.DevolverListaGenero();
}
```

**Capa de Presentación:**
```csharp
// Ref: Presentacion/Form_Paciente.cs → método CargarGeneros()
private void CargarGeneros()
{
    comboBox_Genero.DataSource    = GeneroNegocio.DevolverListasGeneros();
    comboBox_Genero.DisplayMember = "Nombre";   // lo que ve el usuario
    comboBox_Genero.ValueMember   = "Id";       // lo que se guarda
}
```

---

## Resumen: métodos LINQ usados en el proyecto

| Método LINQ | Dónde se usa | Propósito |
|-------------|-------------|-----------|
| `from p in contexto.Paciente select p` | `DevolverListaPaciente()` | Traer todos los pacientes |
| `from g in contexto.Genero select g` | `DevolverListaGenero()` | Traer todos los géneros |
| `FirstOrDefault(p => p.id == id)` | `CargarPacientePorId()`, `Actualizar()`, `Eliminar()` | Buscar por ID |
| `FirstOrDefault(g => g.id == idGenero)` | `DevolverNombreGeneroPorId()` | Buscar género por ID |
| `InsertOnSubmit()` + `SubmitChanges()` | `Nuevo()` | Insertar registro |
| `DeleteOnSubmit()` + `SubmitChanges()` | `EliminarPacientePorID()` | Eliminar registro |

---

## Flujo CRUD completo del sistema

```
Form_Paciente.cs  (Presentación)
      │
      │  llama a
      ▼
PacienteNegocio.cs  (Negocio)
  ├── GuardarPaciente()   → valida → llama a PacienteDatos.Nuevo()
  ├── DevolverListaPaciente()         → PacienteDatos.DevolverListaPaciente()
  ├── CargarPacientePorId(id)         → PacienteDatos.CargarPacientePorID(id)
  └── EliminarPacientePorID(id)       → PacienteDatos.Eliminar(id)
      │
      │  llama a
      ▼
PacienteDatos.cs  (Datos — LINQ to SQL)
  ├── Nuevo()              → InsertOnSubmit + SubmitChanges
  ├── Actualizar()         → FirstOrDefault + SubmitChanges
  ├── DevolverListaPaciente() → from p in contexto.Paciente select p
  ├── CargarPacientePorID()   → FirstOrDefault(p => p.id == id)
  └── Eliminar()           → DeleteOnSubmit + SubmitChanges
      │
      ▼
SQL Server — tablas: Paciente, Genero
```

---

[← CRUD con LINQ](05-crud-linq.md) | [Siguiente: Referencias →](07-referencias.md)

