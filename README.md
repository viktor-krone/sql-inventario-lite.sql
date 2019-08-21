# sql-inventario-lite.sql
modelo BD inventario



Modelo de base de datos del sistema de inventario Inventio Lite
 Mar 15, 2017  Agustin Ramos  1 Comment
En este articulo les explicare un poco de la lógica de la base de datos del sistema de inventario inventio lite y un poco de historia.

Hace poco mas de 18 meses lance la primera versión del sistema Inventio Lite, que al parecer a muchas personas les agrado, poco después lance Inventio Max que a muchos les agrado y eso me lleva a seguir hablando sobre estos maravillosos sistemas.

 

A continuación les dejo una imagen del modelo de la base de datos generada por el programa MySql Workbench.


La base de datos del sistema Inventio Lite esta conformada por 9 tablas las cuales son:

box
category
configuration
operation
operation_type
person
product
sell
user

Ahora  explicare para que sirve cada tabla.

Category
La tabla category sirve para las categorías de los productos del sistema.

Campos

id: Id auto incremental
image: Imagen de la categoría, no implementado
name: Nombre de la categoría, el que se muestra en el sistema
description: Descripción de la categoría
created_at: Fecha de creación
En la tabla product existe un campo llamado category_id que se relaciona con el id de una categoría.

Product
La tabla product es la tabla principal de la base de datos, la tabla product simboliza los productos y cuenta con los siguientes campos:

id: El id o llave primaria del producto
image: se usa para almacenar el nombre de la imagen del producto
barcode: el codigo de barras
name: el nombre del producto
description: una breve descripción del producto
inventary_min: almacena el valor de cantidad mínima en inventario
price_in: el precio de entrada, precio de compra o precio de abastecimiento
price_out: precio de salida o precio de venta
presentation: presentación en bolsa, en botella en caja, etc …
unit: unidad, metros, kilos, libras, onzas, etc.
user_id: almacena el id del usuario que da de alta el producto
category_id: id de la categoría a la cual pertenece el producto
is_active: el producto esta activo o inactivo
created_at: fecha de creacion del producto
Consultas útiles:

Mostrar todos los productos: select * from product
Mostrar productos por category_id: select * from product where category_id=1
Mostrar productos activos: select * from product where is_active=1
Mostrar productos creados por cierto usuario: select * from product where user_id=1
Person
La tabla person simboliza un tipo de persona dentro del sistema, las “personas” por default son: clientes y proveedores definidas por el campo kind, los campos son:

id: id o llave primaria
image: imagen o foto de la persona
name: nombre completo de la persona
lastname: apellidos de la persona
company: nombre de la empresa
address1: dirección o domicilio
address2: dirección o domicilio
phone1: teléfono
phone2: teléfono
email1: correo electrónico
email2: correo electrónico
kind: tipo de persona, 1=cliente, 2=proveedor
created_at: fecha de creación
Consultas útiles

Mostrar clientes: select * from person where kind=1
Mostrar proveedores: select * from person where kind=2
Box
Box sirve para relacionar los cortes de caja.

Campos

id: Id autoincremental
created_at: Fecha de creacion
En la tabla sell existe un campo llamado box_id, entonces cuando se crea una entrada en la tabla box, se asigna el id de la entrada en la tabla box al campo box_id de las “ventas” relacionas.

Operation Type
La tabla operation_type es una biblioteca que contiene los tipos de operación del sistema.

Campos

id: Id autoincremental
name: Nombre del tipo de operación
Esta tabla normalmente va de la mano con la tabla operation.

Los valores de la tabla operation_type estan definidos por default y no se pueden cambiar, en caso de que se cambien pueden afectar el funcionamiento normal del sistema:

1= entrada
2= salida
Sell
La tabla sell es una tabla que agrupara las operaciones, esta relacionado con las ventas o abastecimientos según el operation_type_id los campos de la tabla sell son:

id: el id de la venta o abastecimiento
person_id: el id del cliente para ventas o proveedor en abastecimientos
user_id: el id del usuario que realiza la venta o abastecimiento
operation_type_id: el tipo de operacion, entrada o salida
box_id: el id de caja, siempre es NULL hasta que se crea un corte de caja se le asigna el id del nuevo corte de caja
total: el total de la venta o abastecimiento
cash: el total del pago realizado por el cliente
discount: el descuento en caso de que se aplique
created_at: la fecha de creacion de la entrada
Consultas útiles

Mostrar ventas: select * from sell where operation_type_id=2
Mostrar abastecimientos: select * from sell where operation_type_id=1
Mostrar ventas por corte de caja: select * from sell where operation_type_id=2 and box_id=1
Mostrar ventas por rango de fecha INICIO-FIN (Formato “YYYY-MM-DD”): select * from sell where operation_type_id=2 and (date(created_at)>”INICIO” and date(created_at)<=”FIN”)
Operation
La tabla operation cuenta con los siguientes campos:

id: el id de operación, la llave primaria
product_id: el id del producto de la operación
q: la cantidad de producto
operation_type_id: el tipo de operación, entrada o salida
sell_id: el id de la venta o abastecimiento
created_at: la fecha de creación
Consultas SQL

Sumar total de entrada o abastecimientos por producto:  select sum(q) from operation where operation_type_id=1 and product_id=1;
Sumar total de salidas o ventas por producto:  select sum(q) from operation where operation_type_id=2 and product_id=1;
User
La tabla user sirve para almacenar los usuarios que tienen acceso al sistema, los campos son:

id: id o llave primaria
name: nombre del usuario
lastname: apellido
username: nombre de usuario
email: email
password: contraseña de acceso
image: imagen o foto de perfil
is_active: valor booleano para identificar si esta activo o inactivo
is_admin: valor booleano para identificar si el usuario es administrador
created_at: fecha de creación del usuario
Consultas útiles

Login valido del usuario: select * from user where (email=”EMAIL” and password=”PASSWORD”) and is_active=1
Configuration
La tabla configuration es una tabla experimental que sirve para usar configuraciones o ajustes generales que afectan al funcionamiento del sistema.

Campos

id: Id autoincremental
short: Nombre corto, normalmente se utiliza como una llave para acceder a los valores de las configuraciones en lugar de usar el id
name: Es el nombre de la configuración, este se muestra en el sistema.
kind: Tipo de valor puede ser textual, numérico, si/no, imagen. se usa un valor numérico, por ejemplo, tipo textual=1, numérico=2…
val: Valor almacenado, es el valor referente a la configuración
Las entradas en la tabla de configuración se acceden en el sistema para alterar ciertos funcionamientos.

Conclusión
Como podrán ver el modelo de la base de datos es bastante sencillo.

Al ver el modelo y ver para que sirve cada campo del sistema ustedes podrán modificar la base de datos a sus necesidades.
