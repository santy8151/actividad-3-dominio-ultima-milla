# Actividad 3 · Diseño y dominio

Diseño del flujo **asignar un repartidor a una entrega** para una plataforma logística de última milla.

## Entrega

- [Presentación editable para Canva](presentacion/Actividad-3-Diseno-y-Dominio.pptx)
- [Arquitectura Onion y C4](Architecture.md)
- [Modelo de dominio y glosario](Modelo-Dominio.md)
- [Escenarios BDD](Escenarios-BDD.md)
- [ADR-001](ADR-001.md)
- Código de referencia: [Delivery.cs](Delivery.cs), [AssignmentPorts.cs](AssignmentPorts.cs) y [AssignCourierUseCase.cs](AssignCourierUseCase.cs)

## Arquitectura

El dominio está en el centro. La aplicación define los casos de uso y los puertos. Presentación e infraestructura quedan en el borde y dependen hacia el centro. Los adaptadores implementan los puertos internos.

## Alcance

Es una propuesta de diseño previa a la implementación. No incluye base de datos ni integraciones reales; se dejan para la Actividad 4.

## Sustentación

**Enlace al video:** pendiente de agregar cuando esté publicado.


