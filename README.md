# Solución Prueba Técnica - Desarrollador Senior Delphi

Este repositorio contiene la solución completa a la prueba técnica para el cargo de **Desarrollador Senior Delphi**. Incluye:

- Código fuente en Delphi con prácticas de POO y consumo de APIs REST
- Manejo de concurrencia y tareas paralelas
- Consultas SQL optimizadas
- Patrón DAO con ORM en Delphi
- Grilla personalizada con filtros
- Documentación técnica y diagrama de arquitectura

---

## 🧩 Módulos Implementados

### 1. Consumo de API REST

- Utiliza `TNetHTTPClient` para obtener datos de la API de SpaceX.
- Se aplica el principio de inversión de dependencias usando interfaces (`IHttpClient`, `ISpaceXService`).

### 2. Concurrencia y Paralelismo

- Tres llamadas HTTP ejecutadas en paralelo utilizando `TTask`.
- Uso de `TTask.WaitForAll` para sincronización y despliegue del resultado.

### 3. Consultas SQL Optimizada

- Identificación de pacientes con más de 5 citas atendidas en los últimos 6 meses.
- Paciente con mayor duración acumulada en consultas del último año.
- Sugerencias de índices para mejorar rendimiento.
- Propuesta de denormalización para reportes.

### 4. ORM con DAO

- Implementación del patrón DAO (`IPacienteDAO`) para CRUD y consultas avanzadas con `TFDConnection`.
- Clase de entidad `TPaciente` usada como modelo.

### 5. Grilla con Filtros

- Carga de datos con filtros asincrónicos en `TFDQuery` y sincronización de resultados con `TThread.Synchronize`.

---

## 📊 Diagrama del Sistema

El siguiente diagrama resume el flujo de componentes:

![Arquitectura Delphi](image.png)

---

## 🔧 Requisitos Técnicos

- Delphi con soporte para FireDAC y TTask
- Conexión activa a internet para consumo de APIs
- Base de datos con tablas `Pacientes` y `Citas`

---

## 🚀 Ejecutar la Solución

1. Clonar el repositorio.
2. Abrir el proyecto Delphi.
3. Ejecutar el módulo principal.
4. Verificar resultados en consola o interfaz.

---

## 📁 Estructura del Proyecto

```
├── /src
│   ├── HttpClient.pas
│   ├── SpaceXService.pas
│   ├── PacienteDAO.pas
│   └── MainForm.pas
├── consultas.sql
├── README.md
└── image.png
```

---

## ✍️ Autor

Solución desarrollada como respuesta técnica para la posición de Desarrollador Senior Delphi.