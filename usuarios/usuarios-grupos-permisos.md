# Usuarios, grupos, roles y privilegios

**Empresa:** SolVerde Energía, S.L. (datos ficticios)
**Autor:** Pedro Eduardo De La Puente Rodriguez


## 1. Principio aplicado: mínimo privilegio

Cada persona recibe únicamente los permisos que necesita para su puesto:

- El **comercial** vende, pero solo ve sus propios presupuestos y no accede a Contabilidad.
- El **responsable de ventas** supervisa a todo el equipo comercial, pero no factura.
- **Administración** factura y registra pagos, pero no gestiona ventas.


## 2. Plantilla de usuarios

### 2.1. Usuarios internos

| Usuario | Correo de acceso | Avatar | Puesto | Tipo |
|---|---|---|---|---|
| **Álvaro Gutiérrez Sanz** | alvaro.gutierrez@solverde-energia.example | Foto o avatar con iniciales **AG** | Responsable de ventas | Interno |
| **Lucía Fernández Mora** | lucia.fernandez@solverde-energia.example | Avatar con iniciales **LF** | Comercial | Interno |
| **Javier Morales Ortega** | javier.morales@solverde-energia.example | Avatar con iniciales **JM** | Administración / Facturación | Interno |


### 2.2. Usuario portal (cliente)

| Usuario | Correo de acceso | Cliente al que pertenece | Tipo |
|---|---|---|---|
| **Carmen Ruiz Navarro** | carmen.ruiz@correo-ficticio.example | Cliente particular (instalación de un kit solar en su vivienda) | Portal |


## 3. Tipo de usuario asignado

| Tipo | Qué es | Quién lo tiene aquí |
|---|---|---|
| **Interno** | Empleado con acceso al backend de Odoo, limitado por sus grupos de permisos. Consume licencia. | Álvaro, Lucía y Javier |
| **Portal** | Cliente o proveedor externo. Solo ve sus propios documentos (pedidos, facturas, direcciones) desde el portal `/my`. No entra al backend. | Carmen Ruiz Navarro |
| **Público** | Visitante anónimo de la web, sin sesión iniciada. Odoo lo gestiona con un usuario técnico y no se crea a mano. | Nadie (no procede crearlo) |

**Justificación del portal:** el cliente particular necesita consultar su presupuesto y su factura y poder pagarla, pero no debe ver datos de otros clientes ni la gestión interna de la empresa.


## 4. Grupos y privilegios por usuario y módulo

| Usuario | Puesto | Tipo | Ventas | Facturación / Contabilidad | Inventario | Contactos | Grupo propio | Justificación |
|---|---|---|---|---|---|---|---|---|
| Álvaro Gutiérrez Sanz | Responsable de ventas | Interno | **Administrador** | Sin acceso | Usuario | Acceso estándar | SolVerde Energía / Responsable de ventas *(opcional)* | Debe ver y supervisar todos los presupuestos y pedidos del equipo. Consulta el stock y valida entregas, pero no factura. |
| Lucía Fernández Mora | Comercial | Interno | **Usuario: solo mostrar documentos propios** | **Sin acceso** | Sin acceso | Acceso estándar | **SolVerde Energía / Comercial junior** | Solo gestiona sus propios presupuestos. Sin Contabilidad por principio de mínimo privilegio. |
| Javier Morales Ortega | Administración / Facturación | Interno | **Sin acceso** | **Facturación** (puede facturar y registrar pagos) | Sin acceso | Acceso estándar | SolVerde Energía / Administración *(opcional)* | Emite facturas y cobra, pero no puede crear ni modificar ventas. |
| Carmen Ruiz Navarro | Cliente | Portal | — | — | — | — | — | Solo consulta sus propios pedidos y facturas desde el portal. |


## 5. Grupo propio: SolVerde Energía / Comercial junior

### 5.1. Datos generales

| Campo | Valor |
|---|---|
| Nombre del grupo | **Comercial junior** |
| Aplicación / categoría | **SolVerde Energía** (nombre completo mostrado: *SolVerde Energía / Comercial junior*) |
| Descripción | Perfil comercial de entrada: crea y gestiona sus propios presupuestos, sin acceso a Contabilidad. |
| Usuario asignado | Lucía Fernández Mora |

### 5.2. Grupos heredados («Hereda de» / *Implied groups*)

| Grupo heredado | Efecto |
|---|---|
| **Ventas / Usuario: solo mostrar documentos propios** | Acceso a la app Ventas y visibilidad solo de sus propios presupuestos y pedidos. |
| **Usuario interno** (heredado automáticamente) | Acceso al backend de Odoo. |


### 5.3. Derechos de acceso sobre modelos

**L** = Lectura, **E** = Escritura, **C** = Creación, **B** = Borrado (eliminación).

| Nombre del derecho | Modelo | L | E | C | B | Justificación |
|---|---|:-:|:-:|:-:|:-:|---|
| Comercial junior: pedidos de venta | `sale.order` (Pedido de venta) | ✔ | ✔ | ✔ | ✘ | Puede crear y editar presupuestos, pero no eliminarlos, para conservar el historial comercial. |
| Comercial junior: productos | `product.template` (Plantilla de producto) | ✔ | ✘ | ✘ | ✘ | Puede consultar el catálogo para presupuestar, pero no cambiar precios ni productos. |
| Comercial junior: contactos | `res.partner` (Contacto) | ✔ | ✔ | ✔ | ✘ | Puede dar de alta y actualizar clientes, pero no borrarlos. |

