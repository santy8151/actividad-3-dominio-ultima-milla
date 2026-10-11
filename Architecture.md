# Arquitectura Onion

## Flujo delimitado

`AssignCourierUseCase` asigna un repartidor elegible a una entrega pendiente. El dominio protege la regla de no duplicar una asignación. Application consulta disponibilidad y capacidad mediante puertos, guarda el cambio y registra `DeliveryAssigned`.

## Capas Onion

```text
┌──────────────────────────────────────────────────────────────┐
│ PRESENTACIÓN + INFRAESTRUCTURA                               │
│ API · persistencia · repartidores · rutas · eventos           │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ APLICACIÓN                                              │  │
│  │ AssignCourierUseCase · puertos · contratos              │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │ SERVICIOS Y POLÍTICAS DE DOMINIO                  │  │  │
│  │  │ reglas que coordinan conceptos del negocio        │  │  │
│  │  │  ┌────────────────────────────────────────────┐  │  │  │
│  │  │  │ MODELO DE DOMINIO                          │  │  │  │
│  │  │  │ Delivery · IDs · estados · invariantes     │  │  │  │
│  │  │  └────────────────────────────────────────────┘  │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

**Regla de dependencias:** las referencias de código apuntan hacia el centro. `Domain` no conoce Application, API, ORM ni proveedores externos. Application define `IDeliveryRepository`, `ICourierAvailability`, `IRouteCapacity` e `IAssignmentEvents`. Los adaptadores externos implementan esos contratos. En ejecución, el caso de uso invoca las interfaces inyectadas; la dirección de las dependencias de código se mantiene hacia adentro.

## C4 nivel 1 · Contexto

```mermaid
flowchart LR
  Op[Operador logístico] -->|solicita asignación| Dispatch[Despacho / Asignación]
  Auto[Despacho automático] -->|propone asignación| Dispatch
  Dispatch -->|consulta disponibilidad| Courier[Gestión de repartidores]
  Dispatch -->|consulta capacidad| Routes[Planificación de rutas]
  Dispatch -->|persiste entrega| Delivery[Gestión de entregas]
  Dispatch -->|publica DeliveryAssigned| Routes
```

## C4 nivel 2 · Componentes y dependencias

```mermaid
flowchart LR
  subgraph Outside[Adaptadores externos]
    API[API / Presentación]
    DB[Adaptador de persistencia]
    CourierAdapter[Adaptador de repartidores]
    RouteAdapter[Adaptador de rutas]
    EventAdapter[Adaptador de eventos]
  end
  subgraph Inside[Aplicación y dominio]
    UseCase[AssignCourierUseCase]
    Ports[Puertos definidos por Application]
    Domain[Delivery + reglas del dominio]
  end
  API --> UseCase
  UseCase --> Domain
  UseCase --> Ports
  DB -. implementa .-> Ports
  CourierAdapter -. implementa .-> Ports
  RouteAdapter -. implementa .-> Ports
  EventAdapter -. implementa .-> Ports
```

## Secuencia del caso de uso

1. Cargar `Delivery` por `IDeliveryRepository`.
2. Consultar `ICourierAvailability`.
3. Consultar `IRouteCapacity`.
4. Ejecutar `Delivery.AssignCourier(courierId)`.
5. Guardar por `IDeliveryRepository`.
6. Registrar `DeliveryAssigned` por `IAssignmentEvents`.

Si alguna validación falla, no se modifica la entrega ni se registra el evento. En la Actividad 4 se deberá resolver la atomicidad entre guardar la asignación y publicar el evento (por ejemplo, con outbox), además de evitar carreras entre consultar capacidad y confirmar la asignación.

