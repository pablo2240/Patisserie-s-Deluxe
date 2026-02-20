# PATISSERIE 2.1

## Documentación Técnica de Diagramas UML

------------------------------------------------------------------------

# 1. Diagramas de Casos de Uso

Los diagramas de casos de uso representan la interacción entre los
actores del sistema y sus funcionalidades.

------------------------------------------------------------------------

## 1.1 Invitado

### Funcionalidades

-   Ver catálogo
-   Buscar productos
-   Filtrar productos
-   Ver detalle del producto
-   Registrarse
-   Iniciar sesión

### Código PlantUML

``` plantuml
@startuml
actor Invitado

Invitado --> (Ver catálogo)
Invitado --> (Buscar productos)
Invitado --> (Filtrar productos)
Invitado --> (Ver detalle producto)
Invitado --> (Registrarse)
Invitado --> (Iniciar sesión)

@enduml
```

------------------------------------------------------------------------

## 1.2 Cliente

### Funcionalidades

-   Iniciar sesión
-   Gestionar carrito
-   Realizar pago
-   Recibir factura digital
-   Consultar historial
-   Recibir notificaciones
-   Hacer Reviews

### Código PlantUML

``` plantuml
@startuml
actor Cliente

Cliente --> (Iniciar sesión)
Cliente --> (Ver catálogo)
Cliente --> (Buscar productos)
Cliente --> (Filtrar productos)
Cliente --> (Agregar al carrito)
Cliente --> (Modificar carrito)
Cliente --> (Eliminar producto del carrito)
Cliente --> (Iniciar checkout)
Cliente --> (Realizar pago)
Cliente --> (Recibir factura digital)
Cliente --> (Ver historial de pedidos)
Cliente --> (Recibir notificaciones)
Cliente --> (Consultar estado del pedido)
Cliente --> (Crud Reviews)

@enduml
```

------------------------------------------------------------------------

## 1.3 Domiciliario

### Funcionalidades

-   Ver pedidos asignados
-   Cambiar estado del pedido
-   Marcar como entregado
-   Marcar como no entregado
-   Consultar historial mensual

### Código PlantUML

``` plantuml
@startuml
actor Domiciliario

Domiciliario --> (Iniciar sesión)
Domiciliario --> (Ver pedidos asignados)
Domiciliario --> (Cambiar estado a En Camino)
Domiciliario --> (Marcar como Entregado)
Domiciliario --> (Marcar como No Entregado)
Domiciliario --> (Consultar historial mensual)

@enduml
```

------------------------------------------------------------------------

## 1.4 Administrador

### Funcionalidades

-   Gestionar productos
-   Modificar precios
-   Asignar domiciliarios
-   Gestionar reportes
-   Resolver devoluciones
-   Visualizar dashboard

### Código PlantUML

``` plantuml
@startuml
actor Administrador

Administrador --> (Iniciar sesión)
Administrador --> (Gestionar productos)
Administrador --> (Activar/Inactivar producto)
Administrador --> (Modificar precios)
Administrador --> (Gestionar categorías)
Administrador --> (Ver pedidos)
Administrador --> (Cambiar estado a En Preparación)
Administrador --> (Asignar domiciliario)
Administrador --> (Gestionar reportes RI)
Administrador --> (Devolver pago)
Administrador --> (Devolver compra)
Administrador --> (Ver dashboard)
Administrador --> (Consultar indicadores)

@enduml
```

------------------------------------------------------------------------

# 2. Diagrama de Estados del Pedido

Representa el ciclo de vida completo del pedido dentro del sistema.

## Flujo Principal

PendientePago → Pagado → EnPreparacion → EnCamino → Entregado → Cerrado

## Flujo Alterno

PendientePago → PagoRechazado\
EnCamino → NoEntregado → ReporteGenerado → Cerrado

### Código PlantUML

``` plantuml
@startuml

[*] --> PendientePago

PendientePago --> Pagado
PendientePago --> PagoRechazado

Pagado --> EnPreparacion
EnPreparacion --> EnCamino

EnCamino --> Entregado
EnCamino --> NoEntregado

NoEntregado --> ReporteGenerado
ReporteGenerado --> Cerrado

Entregado --> Cerrado

@enduml
```

------------------------------------------------------------------------

# 3. Diagrama de Clases

Representa la estructura interna del sistema y sus relaciones.

## Clases Principales

-   Usuario
-   Pedido
-   DetallePedido
-   Pago
-   Producto
-   HistorialEstadoPedido
-   ReportePedido
-   Notificacion
-   Reviwes

## Código PlantUML

``` plantuml
@startuml
skinparam classAttributeIconSize 0

class Usuario {
  +Id: int
  +Nombre: string
  +Email: string
  +PasswordHash: string
  +Rol: string
  +BarrioId: int
  +Latitud: decimal
  +Longitud: decimal
  +Activo: bool
}

class Producto {
  +Id: int
  +Nombre: string
  +Precio: decimal
  +Stock: int
  +CompraMaximaPorUsuario: int
  +Activo: bool
}

class Pedido {
  +Id: int
  +NumeroFactura: string
  +Estado: string
  +Subtotal: decimal
  +CostoEnvio: decimal
  +Total: decimal
  +GananciaDomiciliario: decimal
  +DomiciliarioId: int
  +FechaCreacion: datetime
  +RutaFactura: string
}

class DetallePedido {
  +Id: int
  +Cantidad: int
  +PrecioUnitarioHistorico: decimal
}

class Pago {
  +Id: int
  +EstadoPago: string
  +TransactionId: string
  +FechaPago: datetime
}

class HistorialEstadoPedido {
  +Id: int
  +Estado: string
  +FechaCambio: datetime
}

class ReportePedido {
  +Id: int
  +Motivo: string
  +EstadoReporte: string
  +FechaLimite: datetime
}

class Notificacion {
  +Id: int
  +Tipo: string
  +EstadoEnvio: string
  +FechaEnvio: datetime
}

class Reviews {
  +Id: int
  +Calificacion: int
  +Comentario: string
  +Fecha: datetime
}

' Relaciones
Usuario "1" -- "0..*" Pedido : realiza
Usuario "1" -- "0..*" Notificacion : recibe
Usuario "1" -- "0..*" Reviews : escribe
Usuario "1" -- "0..*" HistorialEstadoPedido : registra_accion

Pedido "1" *-- "1..*" DetallePedido
Pedido "1" -- "1" Pago
Pedido "1" -- "0..*" HistorialEstadoPedido
Pedido "1" -- "0..1" ReportePedido
Pedido "1" -- "0..*" Reviews : referencia_compra

Producto "1" -- "0..*" DetallePedido
Producto "1" -- "0..*" Reviews : es_calificado

@enduml
```

------------------------------------------------------------------------

# Conclusión

Los diagramas UML desarrollados para Patisserie 2.1 permiten:

-   Comprender la interacción entre actores.
-   Visualizar el flujo completo del pedido.
-   Entender la estructura interna del sistema.
-   Garantizar coherencia con las reglas de negocio definidas.

Esta documentación sirve como respaldo técnico y académico para el
proyecto.
