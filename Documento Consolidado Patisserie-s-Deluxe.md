
# -----**📖 PATISSERIE-S-DELUXE: Documento Consolidado de Operación, Funcionalidad y Backend**
## **1️⃣ ¿Qué es exactamente Patisserie-s-Deluxe?**
**¿Es una pastelería nueva?** No, es una panadería/pastelería tradicional de barrio (con sede física fija en Medellín) que decidió dar el salto tecnológico. Su objetivo es usar una plataforma web para vender y hacerse famosa en zonas específicas sin abrir nuevos locales físicos.


**¿Es solo venta online?** Sí, para el cliente el canal es 100% digital. Aunque los hornos están en un punto físico, no se atiende público externo para este modelo en el mostrador.


**¿Tiene recogida en tienda?** No. Para simplificar la logística, no se implementará la opción de "Recoger en tienda". Todo lo vendido sale directamente para envío.


**¿Tiene entregas a domicilio obligatorias?** Sí, es la única forma de entrega. El sistema calcula la distancia exacta entre la pastelería y la casa del cliente. El cobro es justo y por kilómetros; esto asegura que el repartidor gane lo justo.

**¿Puede haber pedidos personalizados?** No por ahora. Para que el proyecto sea ágil, se eliminaron los pedidos personalizados, el chat y la edición de ingredientes. Solo se vende lo que está estandarizado en el catálogo.

**¿En qué horario funciona la plataforma (Regla R2)?**

El sistema tiene un horario operativo estricto de **8:00 AM a 7:00 PM**. Fuera de este horario, los clientes pueden ver el catálogo, pero el "Checkout" está bloqueado. Los pedidos que entraron antes del cierre siguen su curso logístico normal.
## -----**2️⃣ ¿Qué hace cada actor en el sistema? (Roles y Funcionalidad)**
**👤 Usuario (El Cliente)**

- **¿Qué puede gestionar?** Registro, Login, Recuperar contraseña y Editar Perfil. **OJO:** Solo puede registrar una única dirección y debe pertenecer estrictamente a la Comuna 11 (Laureles–Estadio) o Comuna 12 (La América).
- **¿Cómo interactúa?** Ve el catálogo , agrega productos al carrito, modifica cantidades y paga vía Wompi.
- **¿Qué hace post-compra?** Hace seguimiento del estado de su pedido, revisa su historial con facturas y tiene un **CRUD de Reviews** (solo puede reseñar si el pedido ya fue entregado, evitando el spam).

**👨‍💼 Administrador (El Operativo - "Encargado")**

- **¿Qué controla?** Tiene el CRUD de productos, categorías y promociones. Gestiona usuarios (bloqueo/activación) y el personal (CRUD de Domiciliarios y otros Admins).
- **¿Cómo maneja la logística?** Cambia los estados del pedido ("En preparación", "Listo para despacho") y asigna los domiciliarios.
- **¿Cómo resuelve problemas (Regla RI)?** Si un pedido no se entrega, el Admin gestiona el **Reporte** desde su panel. Resuelve las devoluciones presenciales en caja (Devolver Pago en efectivo o Devolver Compra si hay stock).
- **¿Qué métricas ve?** Gráficos de ventas semanales, productos más vendidos, y transacciones de Wompi.

**🛵 Domiciliario (El Logístico)**

- **¿Qué ve en su app?** Su hoja de ruta con la lista de pedidos asignados, la dirección del cliente y el mapa.
- **¿Qué acciones toma?** Cambia el estado a "En camino" y "Entregado". Si el cliente no aparece, lo marca como **"No Entregado"** (Activando la regla de reportes RI).
- **¿Cómo gana dinero?** Tiene una "Billetera virtual" donde ve el acumulado mensual de sus ganancias, calculado por los kilómetros recorridos en los pedidos finalizados con éxito.\
  +2
## -----**3️⃣ Flujo de Compra e Inventario (El Motor del Negocio)**
**¿Cómo funciona el carrito de compras (Reglas RN)?**

El usuario agrega productos, pero **el stock se reserva temporalmente por 5 minutos**. Si no paga en ese tiempo, se libera para otros. Además, para evitar acaparamiento, hay un límite de **10 unidades por producto y 20 totales por pedido**.

**¿Cómo es el flujo exacto del pedido?**

1. **Pendiente de Pago:** El usuario confirma el carrito. El sistema suma el costo del domicilio  y **congela el precio** (Inmutabilidad).
1. **Pasarela Wompi:** Redirección para procesar tarjeta, Nequi o PSE. (No hay pago contra entrega por la delicadeza de los postres ).
1. **Pagado/Confirmado:** Wompi envía la señal de éxito (Webhook). Aquí **se descuenta el stock real del inventario**. Si falla, pasa a "Pago Rechazado" y el usuario puede reintentar.
1. **Generación de Factura:** Automáticamente se crea un ID de Factura (ej. PAT-2026-00001) y se envía el PDF inmutable al correo del cliente.
1. **En Preparación / Listo para Entrega:** El Admin hornea y notifica al cliente, luego asigna al domiciliario.
1. **En Camino / Entregado:** El domiciliario lleva el pedido y lo finaliza.

**¿Cómo funciona la distancia del envío?** Se usa la **Fórmula matemática de Haversine**. Toma las coordenadas X (pastelería) e Y (cliente) para un cálculo directo, gratuito y rápido antes de pagar.

**¿Las promociones afectan a todo el mundo?** Afectan al producto individual, pero si una promoción tiene la regla de CompraMaximaPorUsuario = 1 , el sistema bloquea el botón si el usuario ya la compró en el pasado.

## -----
