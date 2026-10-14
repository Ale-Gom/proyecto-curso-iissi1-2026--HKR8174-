# Tienda TCG Pokemon

## Miembros del grupo L3-ABS-1

1. Portela Martín, Manuel
1. López Madejón, Alejandro
1. Gómez Astacio, Alejandro
1. Maria Denti, Gianpaolo

## 1. Introducción al problema

El Juego de Cartas Coleccionables Pokémon vive uno de sus mejores momentos: a marzo de 2026 se habían producido más de 85.000 millones de cartas, en 16 idiomas y en más de 90 países (fuente). El cliente del proyecto es una tienda física especializada en este juego, con una comunidad de coleccionistas, jugadores de torneo y compradores ocasionales. Actualmente la tienda solo vende en el local, controla el stock con una hoja de cálculo que se actualiza a mano y recibe las reservas por WhatsApp e Instagram, lo que provoca ventas de productos agotados y pedidos perdidos. Por su parte, los usuarios intercambian sus cartas repetidas en quedadas, grupos de mensajería y foros, sin un catálogo común que identifique cada carta, sin forma de saber si la otra persona es de fiar y sin seguimiento de los envíos. Esto da lugar a estafas, a cartas ofrecidas a varias personas a la vez y a discusiones sobre su estado de conservación.

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

### 3.2. Usuarios del sistema

## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

#### R.F.01. Catálogo de productos de la tienda

Como cliente o visitante de la tienda quiero listar los productos disponibles filtrando por tipo (sellado, accesorio o carta suelta) y rango de precio, y ordenándolos por coste para encontrar artículos que se ajusten a mis intereses y presupuesto.

##### Prueba de aceptación:

* Verificar que al seleccionar el filtro de tipo (ej. "Sellado") la lista muestra únicamente productos pertenecientes a esa categoría.
* Comprobar que al definir un precio mínimo y máximo solo se devuelven productos cuyo importe se encuentre dentro de dicho rango (ambos inclusive).
* Verificar que el listado resultante permite ordenarse de forma ascendente y descendente según el atributo precio.

**RF-02: Detalle de un producto**
**Como** cliente o visitante **quiero** consultar la ficha detallada de un producto (nombre, precio, stock, imagen y, si es carta suelta, sus datos de catálogo, estado e idioma) **para** evaluar sus características exactas antes de añadirlo al carrito.
**Prueba de aceptación:**

* Comprobar que para cualquier producto (sellado, accesorio o carta suelta) se visualizan correctamente el nombre, el precio actual, el stock disponible y la imagen.
* Verificar que, si el producto es de tipo "Carta suelta", se muestran adicionalmente los datos asociados de la carta del catálogo, su estado de conservación y su idioma.
* Comprobar que, si el producto es "Sellado" o "Accesorio", los campos exclusivos de carta suelta no se muestran en la vista.

**RF-03: Listado de expansiones (Sets)**
**Como** coleccionista o jugador competitivo **quiero** listar las expansiones ordenadas por fecha de lanzamiento y poder filtrar las que son legales en torneos **para** conocer la cronología de las colecciones e identificar qué sets puedo usar en el formato competitivo actual.
**Prueba de aceptación:**

* Comprobar que el listado general de expansiones aparece ordenado cronológicamente por su fecha de lanzamiento.
* Verificar que al activar el filtro de "Legal en torneos", el sistema devuelve únicamente las expansiones cuyo atributo booleano de legalidad sea verdadero (`True`).

---

**RF-04: Cartas del catálogo por expansión**
**Como** coleccionista o jugador **quiero** listar todas las cartas pertenecientes a una expansión concreta, ordenadas por su número de colección y filtradas por rareza o tipo de energía **para** explorar el contenido del set o buscar piezas específicas para mis mazos.
**Prueba de aceptación:**

* Verificar que al seleccionar una expansión solo se listan las cartas vinculadas a dicha colección, ordenadas numéricamente por su número de colección (ej. 001/165, 002/165...).
* Comprobar que al aplicar el filtro de rareza (ej. "Ilustración Especial") o de tipo de energía (ej. "Fuego"), la lista se reduce únicamente a las cartas que coinciden con dichos criterios.
---

**RF-05: Consulta de carpeta de usuario (Inventario)**
**Como** usuario registrado **quiero** consultar la carpeta de un entrenador viendo todas sus cartas registradas con su estado de conservación, idioma y disponibilidad de tradeo **para** gestionar mi propia colección o explorar las cartas que otros usuarios poseen.
**Prueba de aceptación:**

* Comprobar que al acceder a la carpeta de un usuario se listan todas las instancias físicas de cartas asociadas a su identificador.
* Verificar que cada registro de la lista muestra claramente el nombre de la carta, su estado de conservación (Mint, Near Mint, etc.), su idioma y el indicador de si está disponible para intercambio.

---

**RF-06: Búsqueda de ejemplares para tradeo**
**Como** usuario registrado **quiero** seleccionar una carta del catálogo y listar todos los ejemplares físicos que otros usuarios tienen disponibles para tradeo, viendo el nombre y reputación de su dueño ordenados por mejor reputación **para** proponer un intercambio al usuario más fiable.
**Prueba de aceptación:**

* Verificar que dada una carta del catálogo, el listado muestra únicamente ejemplares de usuarios que tengan el atributo `Disponible_Para_Tradeo = True`.
* Comprobar que cada ejemplar listado incluye el nombre del propietario actual, su puntuación de reputación, el estado de conservación de la carta y su idioma.
* Verificar que los resultados aparecen ordenados de forma descendente según la puntuación de reputación del dueño (de 5 a 1 estrellas).

---

**RF-07: Historial de pedidos de un usuario**
**Como** cliente registrado **quiero** listar todos mis pedidos realizados ordenados por fecha, visualizando el importe total y el estado actual **para** llevar un control de mis compras y hacer seguimiento de mis envíos.
**Prueba de aceptación:**

* Comprobar que el usuario solo visualiza los pedidos asociados a su cuenta, ordenados desde el más reciente al más antiguo según la fecha de compra.
* Verificar que en cada fila del listado se muestra el identificador del pedido, la fecha, el importe total y el estado (Pagado, Enviado, Entregado).


---

**RF-08: Detalle de un pedido**
**Como** cliente registrado **quiero** consultar el desglose completo de un pedido (líneas de pedido, productos, cantidades, precios unitarios y total) **para** revisar la factura y verificar qué artículos exactos compré y a qué precio.
**Prueba de aceptación:**

* Verificar que al seleccionar un pedido se muestran todas sus líneas de detalle con el nombre del producto, la cantidad comprada y el precio unitario aplicado en el momento de la compra.
* Comprobar que la suma de los subtotales de cada línea (cantidad × precio unitario) coincide exactamente con el importe total reflejado en la cabecera del pedido.


---

**RF-09: Listado de intercambios (Tradeos)**
**Como** usuario registrado **quiero** listar mis intercambios separando las ofertas que he propuesto de las que he recibido, y pudiendo filtrar por estado **para** gestionar eficazmente mis negociaciones pendientes y revisar mi historial de tratos.
**Prueba de aceptación:**

* Comprobar que la vista permite alternar o separar claramente los intercambios donde el usuario actúa como `Usuario_Ofertante` (propuestos) de aquellos donde actúa como `Usuario_Receptor` (recibidos).
* Verificar que al seleccionar un estado en el filtro (ej. "Pendiente", "Aceptado", "Completado"), la lista se actualiza mostrando solo los intercambios en dicha situación.


---

**RF-10: Detalle de un intercambio**
**Como** usuario participante en un trato **quiero** consultar toda la información detallada de un intercambio (participantes, cartas aportadas por cada parte, estado, fecha, códigos de seguimiento postal y valoraciones) **para** verificar las condiciones del acuerdo, seguir el envío de las cartas y calificar la experiencia.
**Prueba de aceptación:**

* Verificar que la vista de detalle muestra los nombres de ambos participantes (ofertante y receptor), la fecha de creación y el estado actual del intercambio.
* Comprobar que las cartas físicas incluidas en el trato aparecen agrupadas y diferenciadas claramente según a qué participante pertenecen originalmente.
* Verificar que, si el intercambio está en estado "Enviado" o "Completado", se visualizan los códigos de *tracking* de ambos envíos y las valoraciones otorgadas entre los usuarios.


#### 4.1.1. Requisitos de información

##### R.I.01. Título requisito de información

Como [tipo de usuario]
quiero [servicio]
para [razón]

**Prueba de aceptación**
- Descripción de la primera comprobación a realizar
- Descripción de la segunda comprobación a realizar
- ...

#### 4.1.2. Reglas de negocio

##### R.N.01. Stock suficiente
La cantidad de una línea de pedido no puede superar el stock del producto en el momento de la compra. Al pagar el pedido, el stock se reduce en esa cantidad.

##### R.N.02. Participantes y cartas válidos 
El ofertante y el receptor de un intercambio deben ser usuarios distintos. Toda carta incluida debe pertenecer a uno de los dos, y cada uno debe aportar al menos una carta.

##### R.N.03. Bloqueo de cartas 
Solo se pueden añadir cartas marcadas como disponibles para tradeo. Mientras el intercambio esté pendiente, aceptado o enviado, sus cartas pasan a no disponibles y no pueden entrar en otro intercambio. Si se rechaza, vuelven a estar disponibles.

##### R.N.04. Transferencia de propiedad 
Cuando un intercambio pasa a "completado", cada carta pasa a ser propiedad del otro participante. Un intercambio solo puede marcarse como completado si ambos códigos de tracking están registrados.

##### R.N.05. Valoración y reputación 
Cada participante solo puede valorar al otro (de 1 a 5) una vez el intercambio esté completado. La reputación de un usuario es la media de las valoraciones que ha recibido.


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


