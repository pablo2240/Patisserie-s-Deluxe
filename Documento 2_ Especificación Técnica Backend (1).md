
### -----**📄 DOCUMENTO 2: ESPECIFICACIÓN DE REQUISITOS DEL SISTEMA (Funcionales y No Funcionales)**
Este documento detalla las funcionalidades operativas (lo que el sistema debe hacer) y los atributos de calidad (cómo debe comportarse), sirviendo como hoja de ruta técnica para el desarrollo del backend de Patisserie-s-Deluxe.
#### **1. REQUISITOS FUNCIONALES (F)**
Definen las acciones concretas que la API y el sistema deben ejecutar.

- F1. Gestión de Catálogo: El sistema mostrará productos activos ordenados alfabéticamente. El backend debe excluir productos inactivos y enviar una bandera (flag) clara cuando el stock sea 0, para que el frontend muestre la etiqueta "Sin stock". El tiempo de carga de este endpoint debe ser $\le$ 4 segundos en red 4G.
- F2. Búsqueda y Filtrado: La API debe procesar búsquedas de productos de forma insensible a mayúsculas y acentos, combinando filtros de categoría, precio y disponibilidad. La respuesta del servidor debe darse en $\le$ 3 segundos.
- F3. Gestión de Carrito: El backend debe validar que la cantidad solicitada no supere el stock real menos las reservas activas. Debe manejar la lógica de reserva temporal de stock por 5 minutos exactos.
- F4. Seguridad y Autenticación: El sistema permitirá el registro validando el formato del correo electrónico  y que este sea único. Las contraseñas deben ser encriptadas obligatoriamente utilizando BCrypt. La autenticación posterior debe basarse en roles mediante tokens JWT.
- F5. Pasarela de Pagos (Simulación): El backend debe proveer un entorno para la simulación de pago exitoso o fallido , permitiendo un máximo de 3 intentos fallidos antes de bloquear la sesión de pago actual. Cada transacción exitosa generará un TransactionId tipo UUID.
- F6. Facturación y Notificaciones: Inmediatamente después del pago exitoso, el backend desencadenará la generación automática de una factura PDF inmutable bajo el formato profesional PAT-YYYY-XXXXX. El sistema debe registrar esta acción y enviar el correo con el recibo en menos de 5 minutos.
#### **2. REQUISITOS NO FUNCIONALES (NF)**
Definen las restricciones tecnológicas, de seguridad y rendimiento de la arquitectura.

- NF1. Rendimiento: Las respuestas de los endpoints críticos (catálogo, carrito, checkout) deben estar optimizadas para permitir que el frontend logre un rendimiento $\ge$ 80 en Google Lighthouse.
- NF2. Disponibilidad Operativa: El backend debe hacer cumplir la disponibilidad de 8:00 AM – 7:00 PM. Cualquier petición de *checkout* (pago) fuera de este horario debe ser rechazada por la API con un código de error correspondiente (ej. 403 Forbidden).
- NF3. Seguridad de Datos: Todo el tráfico del sistema debe usar HTTPS y se deben incluir prevenciones contra ataques XSS y CSRF. El sistema cumple con lineamientos PCI-DSS al no almacenar datos de tarjetas de crédito  en la base de datos SQL Server.
- NF4. Accesibilidad e Interfaz: Aunque es labor del frontend, la API debe enviar mensajes claros para que el diseño pueda ser *Responsive* con accesibilidad básica  (ej. enviar textos de error precisos para ser leídos por screen-readers).
- NF5. Manejo Seguro de Excepciones: Todos los errores del backend deben ser capturados (manejo seguro de excepciones ). La API jamás debe devolver el *Stack Trace* de .NET o errores de SQL Server al cliente; debe retornar mensajes estandarizados e internamente registrar el error en un log.
- **NF6. Integridad Transaccional:** (Exclusivo backend) Todas las operaciones que involucren cambios de estado de pedido, deducción de inventario, generación de reportes o devoluciones presenciales, deben ejecutarse dentro de transacciones atómicas en SQL Server para evitar inconsistencias de datos.

