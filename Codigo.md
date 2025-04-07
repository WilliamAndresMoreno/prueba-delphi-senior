# Prueba Técnica – Desarrollador Senior Delphi

Esta solución responde a los requerimientos planteados para la vacante de Desarrollador Delphi Senior. Se incluyen ejemplos de código fuente, consultas SQL optimizadas, implementación orientada a objetos, uso de hilos para concurrencia, y una grilla con filtros en segundo plano.

---

## 1. Consumo de API REST con POO

### Interfaces y clases

```delphi
IHttpClient = interface
  ['{GUID}']
  function Get(const URL: string): string;
end;

THttpClient = class(TInterfacedObject, IHttpClient)
public
  function Get(const URL: string): string;
end;

function THttpClient.Get(const URL: string): string;
begin
  with TNetHTTPClient.Create(nil) do
  try
    Result := Get(URL).ContentAsString;
  finally
    Free;
  end;
end;
```

### Servicio SpaceX

```delphi
ISpaceXService = interface
  ['{GUID}']
  function GetLastMissionName: string;
  function GetFormattedDate: string;
end;

TSpaceXService = class(TInterfacedObject, ISpaceXService)
private
  FHttpClient: IHttpClient;
  FJSON: TJSONObject;
public
  constructor Create(AHttpClient: IHttpClient);
  destructor Destroy; override;
  function GetLastMissionName: string;
  function GetFormattedDate: string;
end;

constructor TSpaceXService.Create(AHttpClient: IHttpClient);
var
  LResponse: string;
begin
  FHttpClient := AHttpClient;
  LResponse := FHttpClient.Get('https://api.spacexdata.com/v5/launches/latest');
  FJSON := TJSONObject.ParseJSONValue(LResponse) as TJSONObject;
end;

function TSpaceXService.GetLastMissionName: string;
begin
  Result := FJSON.GetValue<string>('name');
end;

function TSpaceXService.GetFormattedDate: string;
var
  LaunchDate: TDateTime;
begin
  LaunchDate := ISO8601ToDate(FJSON.GetValue<string>('date_utc'));
  Result := FormatDateTime('dd/mm/yyyy', LaunchDate);
end;
```

---

## 2. ⚙️ Concurrencia y Paralelismo

```delphi
procedure LlamadasParalelas;
var
  Results: array [0..2] of string;
  Task1, Task2, Task3: ITask;
begin
  Task1 := TTask.Run(
    procedure
    begin
      Results[0] := TNetHTTPClient.Create(nil).Get('https://jsonplaceholder.typicode.com/todos/1').ContentAsString;
    end);

  Task2 := TTask.Run(
    procedure
    begin
      Results[1] := TNetHTTPClient.Create(nil).Get('https://jsonplaceholder.typicode.com/posts/1').ContentAsString;
    end);

  Task3 := TTask.Run(
    procedure
    begin
      Results[2] := TNetHTTPClient.Create(nil).Get('https://jsonplaceholder.typicode.com/users/1').ContentAsString;
    end);

  TTask.WaitForAll([Task1, Task2, Task3]);
  ShowMessage(Results[0] + sLineBreak + Results[1] + sLineBreak + Results[2]);
end;
```

---

## 3. 🧾 SQL – Consultas y Optimización

### Pacientes con más de 5 citas atendidas últimos 6 meses:

```sql
SELECT P.Id, P.Nombre, COUNT(C.Id) AS TotalCitas,
       AVG(C.Duracion) AS PromedioDuracion
FROM Pacientes P
JOIN Citas C ON P.Id = C.PacienteId
WHERE C.Estado = 'Atendida'
  AND C.Fecha >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
GROUP BY P.Id, P.Nombre
HAVING COUNT(C.Id) > 5;
```

**Índices sugeridos:**

```sql
CREATE INDEX idx_fecha_estado ON Citas (Fecha, Estado);
CREATE INDEX idx_pacienteid ON Citas (PacienteId);
```

### Paciente con mayor tiempo total en citas último año:

```sql
SELECT P.Id, P.Nombre, SUM(C.Duracion) AS TotalTiempo
FROM Pacientes P
JOIN Citas C ON P.Id = C.PacienteId
WHERE C.Fecha >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY P.Id, P.Nombre
ORDER BY TotalTiempo DESC
LIMIT 1;
```

---

## 4. ORM en Delphi con DAO

### Clase `TPaciente`

```delphi
TPaciente = class
public
  Id: Integer;
  Nombre: string;
  Apellido: string;
  FechaNacimiento: TDate;
end;
```

### Interfaz y DAO

```delphi
IPacienteDAO = interface
  procedure Insertar(P: TPaciente);
  procedure Actualizar(P: TPaciente);
  procedure Eliminar(Id: Integer);
  function BuscarPorNombre(Nombre: string): TList<TPaciente>;
  function BuscarPorRangoFecha(Desde, Hasta: TDate): TList<TPaciente>;
end;

TPacienteDAO = class(TInterfacedObject, IPacienteDAO)
private
  FConn: TFDConnection;
public
  constructor Create(AConn: TFDConnection);
  procedure Insertar(P: TPaciente);
  procedure Actualizar(P: TPaciente);
  procedure Eliminar(Id: Integer);
  function BuscarPorNombre(Nombre: string): TList<TPaciente>;
  function BuscarPorRangoFecha(Desde, Hasta: TDate): TList<TPaciente>;
end;
```

---

## 5. 📋 Grilla Personalizada con Filtros

```delphi
procedure CargarDatosConFiltros(Filtro1, Filtro2, Filtro3: string);
begin
  TTask.Run(procedure
  var
    Qry: TFDQuery;
  begin
    Qry := TFDQuery.Create(nil);
    try
      Qry.Connection := MiConexion;
      Qry.SQL.Text := 'SELECT * FROM Citas WHERE Estado = :estado AND Fecha >= :desde AND Fecha <= :hasta';
      Qry.ParamByName('estado').AsString := Filtro1;
      Qry.ParamByName('desde').AsDate := StrToDate(Filtro2);
      Qry.ParamByName('hasta').AsDate := StrToDate(Filtro3);
      Qry.Open;
      TThread.Synchronize(nil,
        procedure
        begin
          MiGrid.DataSource.DataSet := Qry;
        end);
    finally
      // no liberar aquí para que DataSet funcione
    end;
  end);
end;
```

---

## 📌 Notas Finales

- Esta solución está preparada para extenderse con pruebas unitarias, inyección de dependencias y mejoras de arquitectura.
- El código es funcional, claro y modular, usando principios SOLID y buenas prácticas Delphi modernas.
