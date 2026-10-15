# Tienda TCG Pokemon

## Miembros del grupo L3-ABS-1

1. Portela Martín, Manuel
1. López Madejón, Alejandro
1. Gómez Astacio, Alejandro
1. Maria Denti, Gianpaolo

## 1. Introducción al problema

El Juego de Cartas Coleccionables Pokémon vive uno de sus mejores momentos: a marzo de 2026 se habían producido más de 85.000 millones de cartas, en 16 idiomas y en más de 90 países. El cliente del proyecto es una tienda física especializada en este juego, con una comunidad de coleccionistas, jugadores de torneo y compradores ocasionales. Actualmente la tienda solo vende en el local, controla el stock con una hoja de cálculo que se actualiza a mano y recibe las reservas por WhatsApp e Instagram, lo que provoca ventas de productos agotados y pedidos perdidos. Por su parte, los usuarios intercambian sus cartas repetidas en quedadas, grupos de mensajería y foros, sin un catálogo común que identifique cada carta, sin forma de saber si la otra persona es de fiar y sin seguimiento de los envíos. Esto da lugar a estafas, a cartas ofrecidas a varias personas a la vez y a discusiones sobre su estado de conservación.

Con el nuevo sistema, la tienda espera vender online con el stock siempre actualizado, registrar todos los pedidos con su estado y convertirse en el punto de referencia para intercambiar cartas de forma segura. Los usuarios esperan consultar un catálogo con todas las expansiones y cartas, registrar su colección en una carpeta virtual y marcar qué cartas ofrecen para intercambio. También quieren buscar quién tiene una carta concreta, proponerle un intercambio y seguir su estado y su envío hasta completarlo. Para generar confianza, cada usuario tendrá una reputación basada en las valoraciones de sus intercambios completados, y las cartas comprometidas en un trato quedarán bloqueadas para que nadie más pueda pedirlas.


<img width="687" height="321" alt="image" src="https://github.com/user-attachments/assets/8f24dc9e-2a5b-4c1d-b91a-097f5d6740ff" />



## 2. Glosario de términos

### 1. Usuario (Entrenador):

Atributos: ID_Usuario, Nombre, Email, Contraseña, Dirección_Envío, Reputación_Tradeo (puntuación de 1 a 5 estrellas basada en intercambios exitosos), Saldo_Monedero.

### 2. Producto_Tienda:

Atributos: ID_Producto, Nombre (ej. "Caja de Sobres Evoluciones en Paldea", "Funda de Pikachu"), Tipo (Sellado, Accesorio, Carta Suelta), Precio, Stock, Imagen.

Nota: Aquí está el inventario oficial que la tienda vende directamente.

### 3. Pedido:

Atributos: ID_Pedido, ID_Usuario (Clave Foránea), Fecha, Total, Estado (Pagado, Enviado, Entregado).

Relación: Se conecta a los Productos_Tienda mediante una tabla intermedia Detalle_Pedido (clásica de e-commerce).

### 4. Expansión (Set):

Atributos: ID_Expansion, Nombre (ej. "151", "Corona Estelar"), Fecha_Lanzamiento, Icono_Set, Legal_En_Torneos (Booleano).

### 5. Carta_Catálogo:

Atributos: ID_Carta, ID_Expansion (Clave Foránea), Nombre (ej. "Charizard ex"), Número_Colección (ej. "199/165"), Rareza (Común, Rara, Ilustración Especial), Tipo_Energía, URL_Imagen.

Nota: Esta tabla es un "diccionario" inmutable. Representa todas las cartas que existen en el juego, no a quién pertenecen.

### 6. Carta_Usuario (Inventario/Carpeta):

Atributos: ID_Carta_Fisica, ID_Usuario (Dueño actual), ID_Carta_Catalogo (Qué carta es), Estado_Conservación (Mint, Near Mint, Lightly Played, Damaged), Idioma, Disponible_Para_Tradeo (Booleano).

Nota: Esta es la instancia física real de la carta. Si un usuario tiene 3 Pikachus iguales, habrá 3 registros en esta tabla asignados a su ID.

### 7. Intercambio (Trade_Request):

Atributos: ID_Intercambio, ID_Usuario_Ofertante, ID_Usuario_Receptor, Estado_Tradeo (Pendiente, Aceptado, Rechazado, Enviado, Completado, Disputa), Fecha_Creación, Tracking_Ofertante (Código de envío postal), Tracking_Receptor.

Nota: Es la "cabecera" de la propuesta. Fulanito le propone un trato a Menganito.

### 8. Detalle_Intercambio (Trade_Items):

Atributos: ID_Detalle_Tradeo, ID_Intercambio (Clave Foránea), ID_Carta_Fisica (Clave Foránea).

Nota: Aquí se meten las cartas de la tabla Carta_Usuario que están en juego en ese trato. El sistema sabe de quién es cada carta cruzando la información con su dueño actual.


## 3. Visión general del sistema

### 3.1. Requisitos generales

RG-01 – Gestión de usuarios. El sistema deberá permitir registrar y gestionar a los usuarios (entrenadores), con sus datos personales, su dirección de envío, su saldo de monedero, su reputación de tradeo y su colección personal de cartas.

RG-02 – Gestión de la tienda online. El sistema deberá gestionar los productos que vende la tienda (producto sellado, accesorios y cartas sueltas), con su precio, su stock, el catálogo de referencia y los pedidos de los usuarios siempre actualizados.

RG-03 – Gestión de intercambios. El sistema deberá permitir a los usuarios proponer, aceptar o rechazar intercambios de cartas con otros usuarios, hacer su seguimiento hasta completarlos, incluyendo los códigos de envío, y realizar una valoración posterior para calcular la reputación del otro usuario.

### 3.2. Usuarios del sistema

1. Entrenador (usuario registrado). Es el usuario principal del sistema. Puede:
   
  -Consultar la tienda (productos, precios y disponibilidad), el catálogo de expansiones y cartas y las cartas que otros usuarios ofrecen para intercambio.
  
  -Comprar productos de la tienda y consultar el estado de sus pedidos.
  
  -Gestionar su carpeta virtual: registrar sus cartas e indicar su estado, su idioma y si están disponibles para intercambio.
  
  -Proponer intercambios a otros entrenadores, aceptar o rechazar los que recibe, registrar los códigos de envío y valorar a la otra parte al completarlos.
  
  -Consultar su saldo de monedero y su reputación de tradeo.
  
  Dentro de este rol hay tres perfiles típicos, que usan el sistema de forma distinta aunque tengan los mismos permisos:
  
    Coleccionista: busca completar expansiones y conseguir cartas raras concretas, así que usa sobre todo el catálogo y los intercambios.
    
    Jugador competitivo: construye mazos para torneos, así que le interesan las expansiones legales y conseguir cartas concretas rápido.
    
    Comprador ocasional: familias o jugadores nuevos que compran sobre todo producto sellado y accesorios.
    
2. Administrador. Es el dueño o un empleado de la tienda (el cliente del proyecto). Puede:
   
  -Gestionar el catálogo de expansiones y cartas, que solo él puede modificar.
  
  -Gestionar los productos de la tienda, sus precios y su stock.
  
  -Gestionar los pedidos y actualizar su estado (enviado, entregado).
  
  -Mediar en los intercambios en disputa y decidir cómo se resuelven.

## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

#### RF-01: Catálogo de productos de la tienda

Como cliente o visitante de la tienda quiero listar los productos disponibles filtrando por tipo (sellado, accesorio o carta suelta) y rango de precio, y ordenándolos por coste para encontrar artículos que se ajusten a mis intereses y presupuesto.

#### RF-02: Detalle de un producto

Como cliente o visitante quiero consultar la ficha detallada de un producto (nombre, precio, stock, imagen y, si es carta suelta, sus datos de catálogo, estado e idioma) para evaluar sus características exactas antes de añadirlo al carrito.

#### RF-03: Listado de expansiones (Sets)

Como coleccionista o jugador competitivo quiero listar las expansiones ordenadas por fecha de lanzamiento y poder filtrar las que son legales en torneos para conocer la cronología de las colecciones e identificar qué sets puedo usar en el formato competitivo actual.

#### RF-04: Cartas del catálogo por expansión

Como coleccionista o jugador quiero listar todas las cartas pertenecientes a una expansión concreta, ordenadas por su número de colección y filtradas por rareza o tipo de energía para explorar el contenido del set o buscar piezas específicas para mis mazos.

#### RF-05: Consulta de carpeta de usuario (Inventario)
Como usuario registrado quiero consultar la carpeta de un entrenador viendo todas sus cartas registradas con su estado de conservación, idioma y disponibilidad de tradeo para gestionar mi propia colección o explorar las cartas que otros usuarios poseen.

#### RF-06: Búsqueda de ejemplares para tradeo

Como usuario registrado quiero seleccionar una carta del catálogo y listar todos los ejemplares físicos que otros usuarios tienen disponibles para tradeo, viendo el nombre y reputación de su dueño ordenados por mejor reputación para proponer un intercambio al usuario más fiable.

#### RF-07: Historial de pedidos de un usuario

Como cliente registrado quiero listar todos mis pedidos realizados ordenados por fecha, visualizando el importe total y el estado actual para llevar un control de mis compras y hacer seguimiento de mis envíos.

#### RF-08: Detalle de un pedido

Como cliente registrado quiero consultar el desglose completo de un pedido (líneas de pedido, productos, cantidades, precios unitarios y total) para revisar la factura y verificar qué artículos exactos compré y a qué precio.

#### RF-09: Listado de intercambios (Tradeos)

Como usuario registrado quiero listar mis intercambios separando las ofertas que he propuesto de las que he recibido, y pudiendo filtrar por estado para gestionar eficazmente mis negociaciones pendientes y revisar mi historial de tratos.

#### RF-10: Detalle de un intercambio

Como usuario participante en un trato quiero consultar toda la información detallada de un intercambio (participantes, cartas aportadas por cada parte, estado, fecha, códigos de seguimiento postal y valoraciones) para verificar las condiciones del acuerdo, seguir el envío de las cartas y calificar la experiencia.

#### 4.1.1. Requisitos de información

**RI-01: Información de los usuarios**
Como administrador quiero que el sistema almacene la información de los entrenadores registrados (nombre, email, contraseña, dirección de envío, saldo de monedero y reputación de tradeo) para poder identificarlos, enviarles sus pedidos y gestionar sus intercambios.

**R.I.02. Información de los productos de la tienda**
Como administrador quiero que el sistema almacene los productos que vende la tienda (nombre, precio, stock e imagen) y su tipo (sellado, accesorio o carta suelta) para poder ofrecerlos a la venta online.

**R.I.03. Información de los pedidos**
Como entrenador quiero que el sistema almacene mis pedidos (número, fecha, total y estado) y los productos que incluye cada uno, con su cantidad y precio unitario, para poder consultar mis compras y su estado.


**R.I.04. Información de las expansiones**
Como entrenador quiero que el sistema almacene las expansiones del juego (nombre, fecha de lanzamiento, icono y si es legal en torneos) para poder consultar las cartas de cada una y saber cuáles puedo usar en torneo.

**R.I.05. Información del catálogo de cartas**
Como coleccionista quiero que el sistema almacene todas las cartas que existen (nombre, número de colección, rareza, tipo de energía e imagen) y la expansión a la que pertenecen para tener una referencia común con la que identificar cada carta.


**R.I.06. Información de la colección de cada usuario**
Como entrenador quiero que el sistema almacene las cartas físicas que tengo (qué carta del catálogo es, su estado de conservación, su idioma y si está disponible para intercambio) para gestionar mi colección y ofrecer mis cartas repetidas.

**R.I.07. Información de los intercambios**
Como entrenador quiero que el sistema almacene los intercambios que propongo o recibo (fecha de creación, estado, cartas incluidas, usuario ofertante, usuario receptor y códigos de envío de cada parte) para poder seguir cada trato hasta completarlo.

#### 4.1.2. Reglas de negocio

##### R.N.01. Stock suficiente
La cantidad de un producto en una línea de pedido no puede superar el stock disponible de ese producto en el momento de la compra. Al pagar el pedido, el stock se reduce en esa cantidad.

Prueba de aceptación

* Se comprueba que se puede crear un pedido cuando la cantidad pedida es menor o igual que el stock disponible.
* Se comprueba que el sistema rechaza un pedido cuya cantidad supera el stock, e informa al usuario del stock disponible.
* Se comprueba que, al pagar un pedido, el stock de cada producto se reduce exactamente en la cantidad comprada.
* Se comprueba que un producto con stock 0 aparece como agotado y no se puede añadir a un pedido.
* Se comprueba que si dos usuarios compran a la vez la última unidad, solo uno de los pedidos se completa.

##### R.N.02. Participantes y cartas válidos en un intercambio
El ofertante y el receptor de un intercambio deben ser usuarios distintos. Toda carta incluida debe pertenecer a uno de los dos, y cada uno debe aportar al menos una carta.

Prueba de aceptación

* Se comprueba que el sistema no permite que un usuario se proponga un intercambio a sí mismo.
* Se comprueba que el sistema no permite incluir en un intercambio una carta que no pertenece ni al ofertante ni al receptor.
* Se comprueba que el sistema no permite crear un intercambio en el que solo una de las partes aporta cartas.
* Se comprueba que se puede crear un intercambio válido con una o varias cartas de cada participante.

##### R.N.03. Bloqueo de cartas en intercambio
Solo se pueden añadir a un intercambio cartas marcadas como disponibles para tradeo. Mientras el intercambio esté pendiente, aceptado o enviado, sus cartas pasan a no disponibles y no pueden incluirse en otro intercambio. Si el intercambio se rechaza, vuelven a estar disponibles.

Prueba de aceptación

* Se comprueba que el sistema no permite incluir en un intercambio una carta marcada como no disponible para tradeo.
* Se comprueba que, al crear un intercambio, todas sus cartas pasan automáticamente a no disponibles.
* Se comprueba que una carta incluida en un intercambio pendiente, aceptado o enviado no aparece en los listados de cartas disponibles para tradeo.
* Se comprueba que el sistema rechaza un segundo intercambio que incluya una carta ya comprometida en otro intercambio activo.
* Se comprueba que, al rechazar un intercambio, todas sus cartas vuelven a estar disponibles para tradeo.

##### R.N.04. Transferencia de propiedad al completar un intercambio
Cuando un intercambio pasa a completado, cada carta pasa a ser propiedad del otro participante. Un intercambio solo puede marcarse como completado si ambos códigos de envío están registrados.

Prueba de aceptación

* Se comprueba que el sistema no permite marcar como completado un intercambio al que le falta alguno de los dos códigos de envío.
* Se comprueba que, al completar un intercambio, las cartas del ofertante pasan a la carpeta del receptor y viceversa.
* Se comprueba que, tras completarse, las cartas recibidas aparecen como no disponibles para tradeo en la carpeta de su nuevo dueño.
* Se comprueba que en un intercambio rechazado o en disputa ninguna carta cambia de dueño.
* Se comprueba que un intercambio completado ya no puede cambiar de estado.

##### R.N.05. Valoraciones y reputación
Cada participante solo puede valorar al otro, con una puntuación de 1 a 5, una vez el intercambio esté completado. La reputación de un usuario es la media de las valoraciones que ha recibido.

Prueba de aceptación

* Se comprueba que el sistema no permite valorar un intercambio que no está en estado completado.
* Se comprueba que el sistema rechaza valoraciones menores que 1 o mayores que 5.
* Se comprueba que un participante no puede valorar dos veces al otro en el mismo intercambio.
* Se comprueba que, tras una nueva valoración, la reputación del usuario valorado se recalcula como la media de todas sus valoraciones.
* Se comprueba que un usuario sin valoraciones aparece sin reputación, en lugar de con 0 estrellas.

### 4.2. Mapa de historias de usuario (opcional)

### 4.3. Requisitos no funcionales (opcional)

**R.N.F. 01. Título requisito no funcional**
Como [tipo de usuario]
quiero [servicio]
para [razón]

-- fin entregable 1 --

## 5. Modelo conceptual

### 5.1. Diagramas de clases UML

- con restricciones.

### 5.2. Escenarios de prueba

- con descripción textual y diagrama de objetos UML.

## 6. Matrices de trazabilidad

- Matriz de trazabilidad entre los elementos del modelo conceptual y los requisitos.

|       | EntidadX   | AsociaciónX  | RestricciónX  | Entidad2 ...   | 
|:------|:-----------|:-----------|:-----------|:-----------|
| RI-1  | X          | X          | X          | X          |
| RI-2  |            | X          |            | X          |
| RF-1  |            | X          |            | X          |
| RF-2  | X          |            | X          | X          |
| RN-1  |            | X          |            |            |
| RN-2  | X          | X          | X          |            |
| ...   |            |            |            |            |

-- fin entregable 2 --

## 7. Modelo relacional en 3FN

- Relaciones obtenidas al aplicar la transformación del modelo conceptual.

### 7.1.  Justificación de la estrategia de transformación de jerarquías

- si se identificaron jerarquías en el MC.


### 8. Matriz de trazabilidad MC/SQL (opcional):

- Restricciones sobre el MC / Elementos del modelo tecnológico (SQL) (Triggers, checks, etc.)
- Incluir Reglas de negocio — Constraints/Triggers en las matrices de trazabilidad para el entregable 3

|       | EntidadX   | AsociaciónX  | RestricciónX  | Entidad2 ...   | 
|:-------|:-------|:-------|:-------|:-------|
| TABLA-1 |        |        |        |        |
| TABLA-2 |        |        |        |        |
| TABLA-3 |        |        |        |        |
| TABLA-4 |        |        |        |        |
| TRIG-1 |        |        |        |        |
| TRIG-2 | X      | X      |        | X      |
| TRIG-3 |        | X      |        | X      |
| TRIG-4 |        |        | X      |        |
| CONST-1 |        |        |        |        |
| CONST-2 | X      | X      |        | X      |
| CONST-3 |        | X      |        | X      |
| CONST-4 |        |        | X      |        |

Se consideran todo tipo de constraints declarativas (aquellas definidas durante el CREATE TABLE).
-- fin entregable 3 --

## Referencias


