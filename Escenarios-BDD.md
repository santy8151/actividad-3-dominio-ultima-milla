# Escenarios de aceptación · BDD

```gherkin
Característica: Asignación de repartidor a una entrega
  Como operador logístico de última milla
  Quiero asignar un repartidor elegible a una entrega pendiente
  Para despachar el paquete sin duplicidades ni rutas saturadas

  Escenario: Asignación exitosa
    Dado que la entrega "del-001" está pendiente y no tiene repartidor activo
    Y el repartidor "courier-xyz" está disponible
    Y la ruta asociada tiene capacidad
    Cuando se ejecuta AssignCourierUseCase para "del-001" y "courier-xyz"
    Entonces la entrega queda asociada a "courier-xyz"
    Y el estado cambia a "Assigned"
    Y se registra "DeliveryAssigned"

  Escenario: La entrega ya tiene repartidor
    Dado que "del-001" está asignada a "courier-xyz"
    Cuando se intenta asignar "courier-abc"
    Entonces la operación se rechaza con "DeliveryAlreadyAssigned"
    Y la asignación original permanece sin cambios

  Escenario: El repartidor no está disponible
    Dado que la entrega está pendiente y sin repartidor
    Y "courier-xyz" no está disponible
    Cuando se solicita la asignación
    Entonces se rechaza con "CourierUnavailable"
    Y la entrega permanece sin cambios

  Escenario: La ruta está saturada
    Dado que la entrega está pendiente y sin repartidor
    Y la ruta no tiene capacidad disponible
    Cuando se solicita la asignación
    Entonces se rechaza con "RouteCapacityExceeded"
    Y no se registra "DeliveryAssigned"
```
