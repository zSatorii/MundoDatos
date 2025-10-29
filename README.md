# MundoDatos
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