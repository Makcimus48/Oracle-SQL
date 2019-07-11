Условие:
--
 Используются таблицы стандартной схемы OE.
Проанализировать продажи товаров различного статуса в разрезе семейного положения покупателей.
Одной командой SELECT вывести результат в виде таблицы, содержащей пять столбцов:
  * Столбец 1: Статус товара,
  * Столбец 2: Сумма продаж товаров с данным статусом  женатым мужчинам,
  * Столбец 3: Сумма продаж товаров с данным статусом неженатым мужчинам,
  * Столбец 4: Сумма продаж товаров с данным статусом замужним женщинам,
  * Столбец 5: Сумма продаж товаров с данным статусом незамужним женщинам.  
  
То есть строчки относятся к товарам определенного статуса, столбцы – к различному семейному положению, а в ячейках приведены денежные суммы продаж товаров из соответствующих заказов. 
Статус товара определяется по значениям в поле PRODUCT_STATUS таблицы PRODUCT_INFORMATION.
Семейное положение покупателей определить по полям GENDER и MARITAL_STATUS таблицы CUSTOMERS.  

Результат отсортировать по статусу продукта по возрастанию.  

The task:
--
Tables of the standard OE scheme are used.
Analyze the sale of goods of different status in the context of the marital status of buyers.
One SELECT command to display the result in the form of a table containing five columns:
* Column 1: Item Status,
* Column 2: Sum of sales of goods with this status to married men,
* Column 3: Sum of sales of goods with this status to unmarried men,
* Column 4: Sum of sales of goods with this status to married women
* Column 5: Sum of sales of goods with this status to unmarried women.  

That is, the lines refer to the goods of a certain status, the columns refer to different marital status, and the cells contain the amounts of sales of goods from the corresponding orders.
The status of an item is determined by the values in the PRODUCT_STATUS field of the PRODUCT_INFORMATION table.
The marital status of customers is determined by the CENDOMERS table in the GENDER and MARITAL_STATUS fields.  

The result is sorted by product status in ascending order.  

Пример вывода/ Example output:
--
PRODUCT_STATUS|	женат|	не женат | 	замужем |	не замужем
----|----|----|---|----
obsolete      |     	12345,6   |	789101,2      |	345	  |  6789,1
orderable     |      	7890,1    |	23456,7  	|89102,3    |	45678,9
planned       |  	 234567,8      | 	7654    |   		|
under development  |   	9012    	|34567,8   |  	9012 |    	456,7

Решение/ Solution:
--
```
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
