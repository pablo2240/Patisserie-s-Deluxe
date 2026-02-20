
### -----**📄 DOCUMENTO 1: REGLAS DE NEGOCIO Y LÓGICA DEL SISTEMA (PATISSERIE-S-DELUXE)**
Este documento define las restricciones, condiciones y lógicas obligatorias que el backend debe validar en todo momento.

**1. Reglas Generales y de Cuenta**

- **RG-01 (Roles):** Solo existen tres roles en el sistema: Cliente, Administrador y Domiciliario. No existe el rol de invitado para acciones transaccionales.
- **RG-02 (Geolocalización):** Restricción geográfica obligatoria. Los usuarios solo pueden registrarse o editar su dirección si pertenecen a la Comuna 11 (Laureles–Estadio) o Comuna 12 (La América).
- **RG-03 (Dirección Única):** Cada usuario posee únicamente una dirección registrada en el sistema.

**2. Reglas de Catálogo y Carrito**

- **RG-04 (Disponibilidad):** Un producto solo puede agregarse al carrito si su Stock > 0 y su estado Activo = true.
- **RN-01 (Reserva Temporal):** Al agregar al carrito, el stock se reserva por 5 minutos. Expirada la reserva, el stock se libera automáticamente si no se ha iniciado el pago.
- **RN-02 (Límites de Compra):** Un cliente puede comprar un máximo de 10 unidades por producto y 20 unidades en total por pedido para evitar acaparamiento.
- **RC-02 (Promociones):** Si un producto tiene CompraMaximaPorUsuario = 1, el sistema debe validar que el usuario no lo haya comprado previamente en un pedido que ya esté "Entregado".

**3. Reglas de Pago y Facturación**

- **RP-01 (Creación):** Al confirmar el carrito, se crea un Pedido en estado "PendientePago".
- **RP-02 (Cálculos Previos):** Antes de redirigir a la pasarela (Wompi), el sistema debe calcular la DistanciaKm, el CostoEnvio, el TotalFinal y la GananciaDomiciliario.
- **RN-03 (Inmutabilidad de Precio):** El precio se congela al crear el pedido en estado "PendientePago". Este es el precio que se cobra y se factura, aunque el administrador cambie el valor del producto en el catálogo después.
- **RG-05 (Deducción de Inventario):** El stock real se descuenta definitiva y únicamente cuando el pago es aprobado.\
  +1

**4. Control Operativo y de Estados (R2)**

- **R2 (Horario Operativo):** El sistema permitirá nuevos pagos únicamente entre las 8:00 AM y las 7:00 PM. Fuera de este horario el checkout será bloqueado desde backend. Los pedidos generados antes del cierre continuarán su flujo logístico normal.\
  +4
- **TR-06 (Transiciones):** Los estados válidos son: PendientePago, PagoRechazado, Pagado, EnPreparacion, ListoParaEntrega, EnCamino, Entregado, NoEntregado, ReporteGenerado, Cerrado. **No se permiten saltos de estado** en el flujo normal.\
  +2
- **RH-01 (Trazabilidad):** Cada cambio de estado genera un registro obligatorio en el HistorialEstadoPedido, guardando el EstadoAnterior, EstadoNuevo, UsuarioId (quien hizo el cambio) y FechaCambio.

**5. Logística Inversa y Reportes (RI)**

- **RI (Gestión de No Entregados):** Si un pedido pasa a "NoEntregado", se activa la tabla de Reportes y el cliente tiene un plazo de 3 días calendario para hacer una reclamación presencial.
- **Resolución (Caja Física):** El Administrador debe gestionar esto presencialmente con dos opciones:
  - *Devolución de Pago:* Registro manual de entrega de dinero en caja.
  - *Devolución de Compra:* Requiere validación de stock obligatoria; si hay stock se reprograma la entrega física.

**6. Reglas de Domiciliario y Seguridad**

- **RD-01 (Asignación):** Solo se puede asignar un domiciliario si el pedido está en estado "ListoParaEntrega".
- **RD-02 (Remuneración):** La GananciaDomiciliario es igual a DistanciaKm × TarifaPorKm. La billetera mensual es la suma de estas ganancias pero solo de los pedidos en estado "Entregado".
- **RR-01 (Reseñas):** Solo se puede crear una reseña si existe un Pedido en estado "Entregado" que contenga dicho producto.
- **RS (Privilegios):** El Cliente no puede cambiar estados de pedido ; el Domiciliario solo puede cambiar estados de pedidos asignados a él ; y el Administrador gestiona todo el flujo.

