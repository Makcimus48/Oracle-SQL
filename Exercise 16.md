Условие:
--
Связи между вершинами графа заданы символьным выражением, состоящим из двух частей, разделённых символом «->». Каждая часть выражения может содержать произвольное количество вершин графа, перечисленных через запятую. При этом предполагается, что каждая вершина из правой части связана направленным отношением со всеми вершинами, указанными в левой части. Например, запись a,b->c,d означает: a->c, a->d, b->c, b->d.
Создать запрос для определения всех имеющихся циклов.  

The task:
--
The connections between the vertices of the graph are given by a symbolic expression consisting of two parts, separated by the symbol “->”. Each part of the expression can contain an arbitrary number of graph vertices, separated by commas. It is assumed that each vertex from the right side is connected by a directed relation with all the vertices indicated on the left side. For example, the record a, b-> c, d means: a-> c, a-> d, b-> c, b-> d.
Create a query to identify all available cycles.  

Пример входных данных/ Example input data: 
--
|RELATION |  ||
|----|----|----|
|a,b |->| с,d|
|b,d |->| a|
|c |->| b,d|
|d |-> |a,c,e,f|
|f |->| g|
|g |->| h|
|d,h |->| f|


Пример выходных данных/ Example output data:
--
|CYCLE|
|----|
|a->c->b->a|
|b->d->c->b|
|c->d->a->c|
|a->d->c->b->a|


Решение/ Solution:
--
```SQL
SELECT *
FROM (SELECT PRO_I.PRODUCT_STATUS, SUM(ORD_I.UNIT_PRICE * ORD_I.QUANTITY) SUM_QUANT,
      CUST.GENDER||' '||CUST.MARITAL_STATUS G_M_S
      FROM OE.CUSTOMERS CUST JOIN OE.ORDERS ORD
      ON ORD.CUSTOMER_ID = CUST.CUSTOMER_ID JOIN OE.ORDER_ITEMS ORD_I
      ON ORD.ORDER_ID = ORD_I.ORDER_ID JOIN OE.PRODUCT_INFORMATION PRO_I
      ON ORD_I.PRODUCT_ID = PRO_I.PRODUCT_ID
      GROUP BY PRO_I.PRODUCT_STATUS, CUST.GENDER||' '||CUST.MARITAL_STATUS)
PIVOT(SUM(SUM_QUANT) FOR G_M_S IN
      ('M married' AS "женат",
       'M single' AS "не женат",
       'F married' AS "замужем",
       'F single' AS "не замужем"))
ORDER BY PRODUCT_STATUS;
