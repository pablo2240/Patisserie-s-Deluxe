¡Excelente! Llegamos a la última pieza clave de tu documentación. Este **Documento 3** traduce todos esos diagramas técnicos (UML) que creamos antes a un lenguaje natural y estructurado.
### -----**📄 DOCUMENTO 3: CASOS DE USO, FLUJOS DE ESTADO Y ESTRUCTURA DEL SISTEMA (PATISSERIE-S-DELUXE)**
Este documento describe la interacción de los diferentes actores con el sistema, el ciclo de vida transaccional de los pedidos y la estructura de las entidades principales que soportan la lógica de negocio.
#### **1. CASOS DE USO POR ACTOR (Interacciones del Sistema)**
El sistema soporta cuatro niveles de interacción basados en roles. Cada nivel define los permisos y accesos a las funcionalidades del backend.

**1.1. Actor: Invitado (Usuario no autenticado)**

El invitado tiene acceso únicamente a operaciones de lectura (GET) y de creación de identidad (POST para registro).

- **Exploración:** Puede ver el catálogo de productos , buscar por nombre y aplicar filtros (categoría, precio, disponibilidad). Puede acceder al detalle individual de cada producto.
- **Identidad:** Puede registrar una nueva cuenta de Cliente validando su correo electrónico o iniciar sesión si ya posee una.

**1.2. Actor: Cliente (Usuario autenticado)**

El cliente es el motor transaccional del sistema. Hereda los permisos de visualización del Invitado y suma permisos de escritura controlados.

- **Transaccional (Carrito y Pago):** Puede agregar, modificar cantidades o eliminar productos de su carrito. Puede iniciar el proceso de checkout y realizar el pago simulado.
- **Trazabilidad:** Recibe automáticamente su factura digital y notificaciones. Puede consultar su historial de pedidos y ver el estado en tiempo real de una compra activa.
- **Post-venta:** Puede crear, leer, actualizar o eliminar (CRUD) reseñas , siempre que el pedido asociado esté marcado como "Entregado".

**1.3. Actor: Domiciliario (Logística)**

Este actor opera desde una vista móvil o panel simplificado, interactuando exclusivamente con los pedidos que le han sido asignados.

- **Gestión de Entregas:** Puede visualizar la lista de pedidos que le fueron asignados. Es el único autorizado para cambiar el estado del pedido a "En Camino".
- **Cierre de Ruta:** Debe registrar el resultado de la entrega marcando el pedido como "Entregado"  (flujo exitoso) o "No Entregado"  (si el cliente no aparece, detonando la regla RI) .
- **Finanzas:** Puede consultar su historial mensual para verificar la ganancia acumulada en su "billetera".

**1.4. Actor: Administrador (Gestión Total)**

Posee control absoluto sobre el catálogo, el flujo interno de los pedidos y la resolución de conflictos.

- **Gestión de Catálogo:** Puede crear, modificar precios, activar/inactivar productos y gestionar las categorías.
- **Logística Interna:** Visualiza todos los pedidos. Cambia los estados de "Pagado" a "En Preparación" y posteriormente asigna un Domiciliario.\
  +2
- **Resolución (Regla RI):** Accede al panel de Reportes para gestionar los pedidos "No Entregados". Ejecuta los procesos de "Devolver Pago" o "Devolver Compra" tras la visita presencial del cliente.
- **Análisis:** Visualiza el Dashboard general y consulta indicadores de ventas.
#### -----**2. MÁQUINA DE ESTADOS DEL PEDIDO (Ciclo de Vida)**
El backend debe aplicar un patrón de "Máquina de Estados" para el atributo Estado de la tabla Pedido. Ningún estado puede ser saltado y todo cambio debe registrarse en la tabla de Historial.

**Flujo Principal (Happy Path):**

1. **PendientePago:** Estado inicial al confirmar el carrito.
1. **Pagado:** Transición automática tras la confirmación exitosa de la pasarela de pagos (Wompi). Aquí se descuenta el stock.
1. **EnPreparacion:** El Administrador recibe el pedido y cocina los productos.
1. **EnCamino:** El Domiciliario recoge el pedido y se dirige al cliente.
1. **Entregado:** El cliente recibe el producto satisfactoriamente.
1. **Cerrado:** El ciclo finaliza exitosamente.

**Flujos Alternativos (Manejo de Excepciones):**

- **Rechazo Bancario:** PendientePago $\rightarrow$ PagoRechazado. El stock temporal se libera.
- **Fallo de Logística (Regla RI):** EnCamino $\rightarrow$ NoEntregado. Detona la creación de un registro en ReportePedido $\rightarrow$ ReporteGenerado $\rightarrow$ Cerrado (cuando el Admin realiza la devolución en el local).
#### -----**3. ESTRUCTURA LÓGICA DE ENTIDADES (Backend)**
Para soportar los casos de uso y los flujos, la base de datos se estructura bajo los siguientes pilares relacionales:

- **Núcleo de Usuarios:** La tabla Usuario centraliza el acceso. Almacena las coordenadas (Latitud/Longitud) y el BarrioId para validar las comunas 11 y 12. Se relaciona directamente con los Pedidos, Reseñas y Notificaciones.
- **Núcleo Transaccional:** La tabla Pedido es el corazón del sistema. Se compone de múltiples DetallePedido (donde se guarda el *PrecioUnitarioHistorico* para garantizar inmutabilidad) y se relaciona 1:1 con la tabla Pago.
- **Núcleo de Trazabilidad:** Cada pedido tiene una relación obligatoria de "1 a muchos" con la tabla HistorialEstadoPedido. Esto garantiza que cada clic de un Administrador o Domiciliario quede auditado con fecha y usuario responsable.
- **Núcleo Post-Venta:** Si la logística falla, el pedido se asocia (0..1) a la tabla ReportePedido  para gestionar la fecha límite de reclamación. Si es exitosa, los productos adquiridos pueden ser calificados en la tabla Reviews.
### -----**🎯 ¡Listo!**
Con estos tres documentos ya tienes:

1. **Las reglas del juego** (Doc 1: Negocio).
1. **Las metas técnicas** (Doc 2: Funcionales y No Funcionales).
1. **Los flujos y permisos** (Doc 3: Casos de uso y Estados).

