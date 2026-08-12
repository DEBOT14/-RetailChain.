# RetailChain — Consolidación de Inventario con UNION y UNION ALL

## 1. ¿Cuántas filas devuelve cada consulta y por qué son distintas? Explicá con ejemplos concretos qué filas se eliminaron con UNION.
* **`UNION ALL` (Consulta 2):** Devuelve **12 filas** en total (5 de la sucursal Norte + 7 de la sucursal Sur), ya que combina y concatena de forma directa todos los registros sin evaluar similitudes.
* **`UNION` (Consulta 1):** Devuelve menos filas (específicamente **11 filas**) porque aplica un proceso de depuración deduciendo duplicados exactos. 
* *Ejemplo concreto:* Los productos 103 (*Monitor 4K 27*, Computación, stock 5 en Norte y stock 3 en Sur), 104 (*Teclado Mecánico*) y 106 (*SSD Externo 1TB*) están presentes en ambas tablas. Sin embargo, como el stock varía entre una sucursal y otra (ej. el monitor tiene stock 5 en Norte y 3 en Sur), las filas **no son 100% idénticas** en todas sus columnas, por lo que `UNION` conserva ambas. El operador `UNION` solo elimina una fila si absolutamente todos sus campos (`id_producto`, `nombre_producto`, `categoria`, `stock`) coinciden exactamente entre ambas sucursales.

## 2. ¿Por qué UNION ALL es más eficiente que UNION? ¿Qué operación adicional realiza UNION internamente?
`UNION ALL` es computacionalmente más eficiente porque se limita a volcar y concatenar los resultados de ambas consultas de forma secuencial. 
En cambio, `UNION` realiza una **operación de ordenamiento y filtrado de unicidad (Distinct sort)** de manera interna. Esto significa que el motor de base de datos debe almacenar temporalmente el resultado combinado en memoria o disco, ordenarlo y escanearlo para eliminar los duplicados exactos antes de entregar el resultado final, lo cual consume más CPU y memoria, especialmente con grandes volúmenes de datos.

## 3. ¿En qué casos de negocio usarías cada uno? Dá al menos dos ejemplos reales distintos a los del ejercicio.
* **Casos para `UNION`:**
  1. *Listado de correos para newsletter corporativo:* Al consolidar bases de datos de clientes de distintas filiales, se usa `UNION` para asegurar que si un cliente está registrado en dos sucursales con exactamente los mismos datos, reciba el correo una sola vez.
  2. *Catálogo maestro de precios globales:* Para unificar listas de precios provenientes de canales online y físicos donde se requiere mostrar un listado limpio de productos únicos disponibles.
* **Casos para `UNION ALL`:**
  1. *Reporte histórico de transacciones o auditoría de tickets:* Al auditar la cantidad total de operaciones de venta realizadas en múltiples tiendas durante un día festivo, necesitamos conservar cada ticket de manera independiente sin importar si dos transacciones tienen montos y productos idénticos.
  2. *Monitoreo de logs de servidores:* Para consolidar registros de eventos o errores provenientes de diferentes nodos de una aplicación web, donde cada evento de registro (log) es único por su marca de tiempo exacta.

## 4. ¿Qué pasa si las columnas de ambas consultas no coinciden en número o tipo? ¿Qué error genera SQL?
Para que cualquier operación de conjunto (`UNION` o `UNION ALL`) funcione, se deben cumplir dos reglas estructurales estrictas:
1. Las consultas deben tener **el mismo número de columnas**.
2. Los tipos de datos de las columnas correspondientes deben ser **compatibles** (o convertibles implícitamente por el motor).

Si estas reglas se incumplen, el motor de base de datos abortará la ejecución y generará un **error de sintaxis o de tipos** (por ejemplo, indicando que las consultas tienen un número diferente de columnas o que no se pueden convertir tipos de datos como texto a entero).
