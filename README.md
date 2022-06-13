#  -DESAFIO 8 - MONGO DB 

### Crear una base de datos llamada ecommerce

`$ >use ecommerce`

Muestra bases vacías sin colecciones 
`$ >db `

Muestra bases con colecciones
`$ >show dbs`

Muestra colecciones 
`$ >show collections`

## 1 - Agregar 10 documentos con valores distintos a las colecciones mensajes y productos. El formato debe ser similar al que venimos usando con la bd mariadb.

`$ >db.mensajesl.insertMany([{"author":" Roberto", "texto":"Hola"},{"author":"Juan","texto":"Buen día"},
	{"author":"María","texto":"Buen día"},{"author":"Luis","texto":"Buenas tardes"},{"author":"Carla","texto":"Hola, que tal!"},{"author":"Lucia","texto":"Buenas noches"},{"author":"Ricardo","texto":"Buen día"},
	{"author":"Pedro","texto":"Buenas tardes"},{"author":"Desiree","texto":"Buenas noches"},{"author":"Carmen","texto":"Hola, como estás?"}])`

`$ >db.articulos.insertMany([{“title”: “Buzo Nike”, “price”:120", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978263/buzo-nike-dri-fit-academy.jpg ","stock":13},{“title”: “Buzo Lotto”, “price”:580", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978263/buzo-lotto-smart.jpg","stock":15},
{“title”: “Buzo Nike JDI”, “price”:900", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978263/buzo-nike-sportswear-jdi.jpg","stock":21},{“title”: “Buzo Adidas”, “price”:1280", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978263/buzo-adidas-essentials-logo.jpg","stock":32},{“title”: “Buzo Fila”, “price”:1700", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978263/buzo-fila-animal.jpg","stock":9},
{“title”: “Campera Adidas”, “price”:2300", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978248/campera-adidas-tokyo-run.jpg","stock":13},{“title”: “Campera Nike”, “price”:2860", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978248/campera-nike-repel-miler.jpg","stock":7},{“title”: “Campera Fila”, “price”:3350", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978248/campera-fila-go-to-mars.jpg","stock":19},{“title”: “Buzo Nike Sportwear”, “price”:4320", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978263/buzo-nike-sportswear-essentials.jpg","stock":3},{“title”: “Remera Puma”, “price”:4990", "image": "https://res.cloudinary.com/ojedaweb/image/upload/v1647978232/remera-puma-essentials-heather.jpg 95 ",
"stock":6}])`

## 2 - Definir las claves de los documentos en relación a los campos de las tablas de esa base.

`$ >db.articulos.createIndex({"title": 1})`
`$ >db.mensajes.createIndex({"author": 1})`

En el caso de productos, poner valores al campo precio entre los 100 y 5000 pesos (eligiendo valores intermedios; ej: 120, 580,900, 1280, 1700, 2300, 2860, 3350 , 4320, 4990)

Se realizo al insertar los articulos en punto 1) 


## 3 - Listar todos los documentos en cada colección

`$ >db.mensajes.find().pretty()` 
`$ >db.articulos.find().pretty()`

## 4 - Mostrar la cantidad de documentos almacenados en cada una de ellas.

`$ >db.mensajes.estimatedDocumentCount()`
`$ >db.articulos.estimatedDocumentCount()`

## 5 - Realizar un CRUD sobre la colección de productos:
### a) Agregar un producto más en la colección de productos.

`$>db.articulos.insertOne("title":"Producto 1", "price": 4120, "image":"htts://image/producto1.jpg", "stock": 10).pretty()`

### b) Realizar una consulta por nombre de producto específico:

`$ >db.articulos.findOne({"title": "Buzo Nike"}).pretty()`  
  
 ####   i) Listar los productos con precio menor a 1000 pesos.
     
     `$ >db.articulos.find({"price": {$lt: 1000} } ).pretty()`

####   ii) Listar los productos con precio entre los 1000 a 3000 pesos.

	`$ >db.articulos.find( { $and: [ {"price": {$gte: 1000 } } , {"price":  { $lte: 3000}  } ] } ).pretty()`

####  iii) Listar los productos con precio mayor a 3000 pesos

	`$ >db.articulos.find({"price": {$gt: 3000  } } ).pretty()`

####  iv) Realizar una consulta que traiga solo el nombre del tercer producto más barato
	
    `$ >db.articulos.aggregate(  [  {  $group: {  _id: {},   minPrice : { $min: "$price" }  } }  ] )`

### c) Hacer una actualización sobre todos los productos con precios  mayores a 4000 pesos.
	
    `$ >db.articulos.updateMany({ "price" : {$gt 4000 } } , {$max : { "price" : 6000 } }  ).pretty()`

### d) Cambiar el stock a cero de los productos con precios mayores a 4000 pesos

	`$ >db.articulos.updateMany({"price" : {$gt 4000 } } , {$set : { "stock" : 0} } ).pretty()`

### e) Borrar los productos con precio menor a 1000 pesos
	
    `$ >db.articulos.deleteMany({"price": {$lt: 1000} } ).pretty()`

### 6) Crear un usuario 'pepe' clave: 'asd456' que sólo pueda leer la base de datos ecommerce

Verificar que pepe no pueda cambiar información. CREAR UN  USUARIO LECTOR

`$> db.createUser( { user: "pepe", pwd: "asd456", roles: [ { role: "read", db:"ecommerce"} ]  } )`

ingresar con usuario lector 
`$ >mongo -u pepe -p asd456`

ingresar a la bd
`$ >use ecommerce`

verificar acceso a lectura para ambas colecciones 

`$> db.mensajes.find().pretty()`

`$ >db.articulos.find().pretty()`

Pruebas para comprobar que no permite escribir en ambas colecciones
`$ >db.mensajes.insertOne({"author":"Macarena", "texto":"Hola" }) `

`$ >db.articulos.insertOne({"title": "Producto A", "price": 155, "image": "https://image/productoA.jpg", "stock": 1 })`


