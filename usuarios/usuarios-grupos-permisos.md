# Usuarios, grupos, roles y privilegios

**Empresa:** SolVerde Energía, S.L. (datos ficticios)
**Autor:** Pedro Eduardo De La Puente Rodriguez

> Todos los nombres, correos y contraseñas de este documento son inventados y solo sirven para la práctica.

## 1. Principio aplicado: mínimo privilegio

Cada persona recibe únicamente los permisos que necesita para su puesto:

- El **comercial** vende, pero solo ve sus propios presupuestos y no accede a Contabilidad.
- El **responsable de ventas** supervisa a todo el equipo comercial, pero no factura.
- **Administración** factura y registra pagos, pero no gestiona ventas.

---

## 2. Plantilla de usuarios

### 2.1. Usuarios internos

| Usuario | Correo de acceso | Avatar | Puesto | Tipo |
|---|---|---|---|---|
| **Álvaro Gutiérrez Sanz** | alvaro.gutierrez@solverde-energia.example | Foto o avatar con iniciales **AG** | Responsable de ventas | Interno |
| **Lucía Fernández Mora** | lucia.fernandez@solverde-energia.example | Avatar con iniciales **LF** | Comercial | Interno |
| **Javier Morales Ortega** | javier.morales@solverde-energia.example | Avatar con iniciales **JM** | Administración / Facturación | Interno |

> Contraseña de pruebas sugerida para los tres: `SolVerde#2026` (solo para esta práctica).

### 2.2. Usuario portal (cliente)

| Usuario | Correo de acceso | Cliente al que pertenece | Tipo |
|---|---|---|---|
| **Carmen Ruiz Navarro** | carmen.ruiz@correo-ficticio.example | Cliente particular (instalación de un kit solar en su vivienda) | Portal |

> Usa el cliente particular que hayas creado en el apartado 3 de la práctica. Si tus clientes se llaman distinto, cambia aquí el nombre y el correo.

---

## 3. Tipo de usuario asignado

| Tipo | Qué es | Quién lo tiene aquí |
|---|---|---|
| **Interno** | Empleado con acceso al backend de Odoo, limitado por sus grupos de permisos. Consume licencia. | Álvaro, Lucía y Javier |
| **Portal** | Cliente o proveedor externo. Solo ve sus propios documentos (pedidos, facturas, direcciones) desde el portal `/my`. No entra al backend. | Carmen Ruiz Navarro |
| **Público** | Visitante anónimo de la web, sin sesión iniciada. Odoo lo gestiona con un usuario técnico y no se crea a mano. | Nadie (no procede crearlo) |

**Justificación del portal:** el cliente particular necesita consultar su presupuesto y su factura y poder pagarla, pero no debe ver datos de otros clientes ni la gestión interna de la empresa.

---

## 4. Grupos y privilegios por usuario y módulo

| Usuario | Puesto | Tipo | Ventas | Facturación / Contabilidad | Inventario | Contactos | Grupo propio | Justificación |
|---|---|---|---|---|---|---|---|---|
| Álvaro Gutiérrez Sanz | Responsable de ventas | Interno | **Administrador** | Sin acceso | Usuario | Acceso estándar | SolVerde Energía / Responsable de ventas *(opcional)* | Debe ver y supervisar todos los presupuestos y pedidos del equipo. Consulta el stock y valida entregas, pero no factura. |
| Lucía Fernández Mora | Comercial | Interno | **Usuario: solo mostrar documentos propios** | **Sin acceso** | Sin acceso | Acceso estándar | **SolVerde Energía / Comercial junior** | Solo gestiona sus propios presupuestos. Sin Contabilidad por principio de mínimo privilegio. |
| Javier Morales Ortega | Administración / Facturación | Interno | **Sin acceso** | **Facturación** (puede facturar y registrar pagos) | Sin acceso | Acceso estándar | SolVerde Energía / Administración *(opcional)* | Emite facturas y cobra, pero no puede crear ni modificar ventas. |
| Carmen Ruiz Navarro | Cliente | Portal | — | — | — | — | — | Solo consulta sus propios pedidos y facturas desde el portal. |

> Los nombres exactos de los privilegios pueden variar un poco según la versión de Odoo (por ejemplo, «Facturación» puede aparecer como *Billing*). Elige siempre el nivel que no incluye gestión de contabilidad completa.

---

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

> No hereda ningún grupo de Contabilidad/Facturación ni de Administrador de ventas.

### 5.3. Derechos de acceso sobre modelos

**L** = Lectura, **E** = Escritura, **C** = Creación, **B** = Borrado (eliminación).

| Nombre del derecho | Modelo | L | E | C | B | Justificación |
|---|---|:-:|:-:|:-:|:-:|---|
| Comercial junior: pedidos de venta | `sale.order` (Pedido de venta) | ✔ | ✔ | ✔ | ✘ | Puede crear y editar presupuestos, pero no eliminarlos, para conservar el historial comercial. |
| Comercial junior: productos | `product.template` (Plantilla de producto) | ✔ | ✘ | ✘ | ✘ | Puede consultar el catálogo para presupuestar, pero no cambiar precios ni productos. |
| Comercial junior: contactos | `res.partner` (Contacto) | ✔ | ✔ | ✔ | ✘ | Puede dar de alta y actualizar clientes, pero no borrarlos. |

> Estos derechos se **suman** a los que ya trae el grupo heredado. Los que dejan una casilla sin marcar (✘) refuerzan lo que no puede hacer, que se comprobará en la demostración.

### 5.4. Menús y reglas de registro (opcional)

| Elemento | Configuración |
|---|---|
| Menús | Sin menús extra. El grupo no da acceso a Contabilidad/Facturación. |
| Regla de registro | Se mantiene la regla estándar de Ventas: solo ve los presupuestos de los que es comercial (`user_id = usuario actual`). |

---

## 6. Demostración de que los permisos funcionan

Iniciar sesión con cada usuario (puedes usar ventanas de incógnito) y hacer las comprobaciones. Anota y captura pantalla de cada una.

### 6.1. Lucía Fernández Mora (Comercial)

| Prueba | Resultado esperado |
|---|---|
| Abrir el menú de aplicaciones | Ve Ventas (y Contactos), pero **no** Contabilidad/Facturación. |
| Crear un presupuesto | ✔ Permitido. |
| Ver los presupuestos de otros comerciales | ✘ Solo ve los suyos. |
| Eliminar un pedido de venta | ✘ No aparece la opción *Eliminar*. |
| Eliminar un cliente | ✘ No puede borrarlo. |
| Modificar el precio de un producto del catálogo | ✘ Solo lectura. |

### 6.2. Álvaro Gutiérrez Sanz (Responsable de ventas)

| Prueba | Resultado esperado |
|---|---|
| Ver los presupuestos de Lucía | ✔ Ve todos los presupuestos y pedidos. |
| Acceso a informes de ventas de todo el equipo | ✔ Permitido. |
| Abrir el menú de Contabilidad/Facturación | ✘ No aparece. |
| Consultar stock en Inventario | ✔ Permitido. |

### 6.3. Javier Morales Ortega (Administración)

| Prueba | Resultado esperado |
|---|---|
| Abrir Facturación | ✔ Aparece el menú. |
| Crear una factura y registrar un pago | ✔ Permitido. |
| Abrir el menú de Ventas | ✘ No aparece. |
| Crear un presupuesto de venta | ✘ Sin acceso. |

### 6.4. Carmen Ruiz Navarro (Portal)

| Prueba | Resultado esperado |
|---|---|
| Iniciar sesión | Entra a `/my` (portal). No al backend. |
| Ver sus pedidos y facturas | ✔ Solo los suyos. |
| Acceder a `/odoo` | ✘ No tiene acceso. |

---

## 7. Pasos para crearlo en Odoo

1. **Activar el modo desarrollador:** Ajustes → *Activar el modo de desarrollador*.
2. **Crear el grupo propio:** Ajustes → Usuarios y compañías → **Grupos** → Nuevo.
   - Nombre: `Comercial junior` y aplicación/categoría: `SolVerde Energía`.
   - Pestaña *Heredado*: añadir *Ventas / Usuario: solo mostrar documentos propios*.
   - Pestaña *Derechos de acceso*: añadir las tres líneas del apartado 5.3.
3. **Crear los usuarios:** Ajustes → Usuarios y compañías → **Usuarios** → Nuevo.
   - Rellenar nombre, correo de acceso, foto/avatar y contraseña.
   - Pestaña *Derechos de acceso*: aplicar la tabla del apartado 4.
   - A Lucía asignarle además el grupo **SolVerde Energía / Comercial junior**.
4. **Crear el usuario portal:** abrir la ficha del cliente → *Acciones* → **Conceder acceso al portal** (o crear el usuario con tipo *Portal*).
5. **Comprobar:** hacer el apartado 6 y guardar las capturas.
