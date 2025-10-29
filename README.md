# Análisis conceptual – Devart
# ¿Qué problema principal busca resolver la normalización en una base de datos y por qué es crítica en sistemas empresariales?
    La normalizacion de busca resolver el problema de la organizacion de los datos, y mas para empresas grandes donde guardan muchos datos y esto se vuelve un caos
    
    La critica se vuelve principalmente en la actualizacion de datos ya que si se olvida uno se tienen datos incompletos
    No se pueded agregar un nuevo producto al catalogo hasta que alguien lo pida o lo compre porque la infomacion esta mezclada con la informacion de pedidos
    y si se llega a eliminar un ultimo pedido de un cliente se puede perder la informacion historica de ese cliente


# Describe con tus propias palabras las diferencias entre 1NF, 2NF y 3NF según los ejemplos del artículo.
    El 1NF es por asi decirlo un dato, una celda, como que cada celda debe tener un solo tipo de una cosa sin mezclarlas
    el ejemplo de Devart, tenían un campo "Customer Info" que contenía: "John Doe; 555-1234, 555-5678; 123 Maple St, New York, 456 Elm St, Chicago; 1985-07-04; [email protected]".

    El 2NF elimina la informacion que es repetida
     En el ejemplo de Devart, tenían una tabla OrderItems con una clave compuesta (OrderID + ItemID), pero también almacenaban ItemName, Category y Price en esa tabla.​

    El 3NF Se asegura de que cada dato dependa directamente de el tema de la tabla
    En el ejemplo original de Devart, la tabla Orders contenía DeliveryAddress y City

# En los ejemplos de Devart, identifica una situación donde la normalización mejora la integridad de datos, pero podría afectar el rendimiento. Explica el motivo.
OrderID | ItemName | Category | Price | Quantity
1       | Tea      | Beverage | 1.50  | 2
2       | Tea      | Beverage | 1.50  | 1
Problema de integridad: Si cambias el precio del te, debes actualizar múltiples registros. Riesgo de inconsistencias ("Tea" vs "tea" vs "Te")

Despues del 2NF se crean dos tablas separadas que son items y orderitems
de esta manera cada producto existe una sola vez y actualizar el precio se hace en un solo registro

y el problema del 3NF Las direcciones de entrega estaban en la tabla Orders, repitiendose cada vez que un cliente hacia un pedido a la misma direccion y la solucion para esta es crear una tabla separada de customerid

# ¿Qué papel juegan las dependencias funcionales en el proceso de normalización y cómo las identificarías en una tabla?
Las dependencias funcionales son relaciones entre columnas de determinacion es decir A -> B es decir que a determina b
y el papel que juega en estas es lo que guia cada proceso de normalizacion
y para identificarlas se identifica primero la clave primaria, se revisan columna por columna, se buscan dependencias parciales, dependencias transitivas

# Explica, con tus palabras, cuándo sería justificable “desnormalizar” una base de datos según el contexto de negocio.
es justificable cuando la velocidad de consulta es mas critica que tener los datos perfectamente organizados
tambien por limitaciones tecnicas, o en un caso de reportar en tiempo real ya que calcular totales y agregaciones es muy ineficiente entonces en ese caso es mejor almacenar valores pre calculados



# 2

CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_phone VARCHAR(50),
    customer_email VARCHAR(100)
);

CREATE TABLE items (
    item_id INT PRIMARY KEY,
    item_name VARCHAR(100) NOT NULL,
    item_price DECIMAL(10,2) NOT NULL
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
    order_id INT,
    item_id INT,
    PRIMARY KEY (order_id, item_id),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (item_id) REFERENCES items(item_id)
);
este codigo se usa para crear las tablas bien
y este
INSERT INTO customers (customer_id, customer_phone, customer_email)
SELECT DISTINCT customer_id, customer_phone, customer_email
FROM forniture_sales;

INSERT INTO items (item_id, item_name, item_price)
SELECT DISTINCT item_id, item_name, item_price FROM (
  SELECT item_1_id AS item_id, item_1_name AS item_name, item_1_price AS item_price FROM forniture_sales
  UNION
  SELECT item_2_id, item_2_name, item_2_price FROM forniture_sales
  UNION
  SELECT item_3_id, item_3_name, item_3_price FROM forniture_sales
) productos
WHERE item_id IS NOT NULL;

INSERT INTO orders (order_id, order_date, customer_id)
SELECT order_id, order_date, customer_id
FROM forniture_sales;

INSERT INTO order_items (order_id, item_id)
SELECT order_id, item_1_id FROM forniture_sales WHERE item_1_id IS NOT NULL;
INSERT INTO order_items (order_id, item_id)
SELECT order_id, item_2_id FROM forniture_sales WHERE item_2_id IS NOT NULL;
INSERT INTO order_items (order_id, item_id)
SELECT order_id, item_3_id FROM forniture_sales WHERE item_3_id IS NOT NULL;
para migrar los datos de el sql de fourniture sales, esto esta aplicando las reglas de 1nf 2nf 3nf


# 3 Biblioteca proyecto
yCREATE TABLE usuarios (
    usuario_id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    tipo ENUM('Estudiante', 'Profesor', 'Publico') NOT NULL
);

CREATE TABLE libros (
    libro_id INT PRIMARY KEY AUTO_INCREMENT,
    isbn VARCHAR(13) UNIQUE NOT NULL,
    titulo VARCHAR(200) NOT NULL,
    autor VARCHAR(150) NOT NULL
);

CREATE TABLE prestamos (
    prestamo_id INT PRIMARY KEY AUTO_INCREMENT,
    usuario_id INT NOT NULL,
    libro_id INT NOT NULL,
    fecha_prestamo DATE NOT NULL,
    fecha_devolucion_esperada DATE NOT NULL,
    fecha_devolucion_real DATE,
    
    FOREIGN KEY (usuario_id) REFERENCES usuarios(usuario_id),
    FOREIGN KEY (libro_id) REFERENCES libros(libro_id)
);

CREATE TABLE multas (
    multa_id INT PRIMARY KEY AUTO_INCREMENT,
    prestamo_id INT NOT NULL,
    dias_retraso INT NOT NULL,
    monto DECIMAL(10,2) NOT NULL,
    pagado BOOLEAN DEFAULT FALSE,
    
    FOREIGN KEY (prestamo_id) REFERENCES prestamos(prestamo_id)
);

INSERT INTO usuarios (nombre, email, tipo) VALUES
('María García', 'maria@email.com', 'Estudiante'),
('Carlos Pérez', 'carlos@email.com', 'Profesor'),
('Ana López', 'ana@email.com', 'Publico');

INSERT INTO libros (isbn, titulo, autor) VALUES
('9780061120084', 'Cien años de soledad', 'Gabriel García Márquez'),
('9788497592208', 'Sapiens', 'Yuval Noah Harari'),
('9780735619678', 'Código Limpio', 'Robert C. Martin');

INSERT INTO prestamos (usuario_id, libro_id, fecha_prestamo, fecha_devolucion_esperada) VALUES
(1, 1, '2025-10-15', '2025-10-30'),
(2, 2, '2025-10-10', '2025-11-09'),
(3, 3, '2025-09-20', '2025-09-27');

INSERT INTO multas (prestamo_id, dias_retraso, monto, pagado) VALUES
(3, 8, 16000.00, FALSE);
