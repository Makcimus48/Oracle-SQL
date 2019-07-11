Условие:
--
Используются таблицы стандартной схемы OE.
Одной командой SELECT вывести информацию о суммарной стоимости товаров, имеющихся в наличии на складах компании. 
При расчете суммарной стоимости использовать цену товаров по каталогу.
Вывести информацию только по тем складам, где есть товары.  

В результат вывести три столбца:
1. Идентификатор товарного склада,
2. Название товарного склада,
3. Суммарная стоимость товаров, имеющихся в наличии на складе (округлить до целых).  

Результат отсортировать:
1. По суммарной стоимости товаров, имеющихся в наличии на складе (по убыванию),
2. По названию товарного склада (по возрастанию).  

The task:
--
Tables of the standard OE scheme are used.
One SELECT command to display information about the total value of goods available in the company's warehouses.
When calculating the total cost, use the price of the goods in the catalog.
Display information only on those warehouses where there are goods.  

In the result display three columns:
1. Identifier of the warehouse,
2. The name of the warehouse,
3. The total value of goods in stock (rounded to the nearest).  

Result to sort:
1. According to the total value of goods in stock (descending),
2. According to the name of the warehouse (ascending).  

Решение/ Solution:
--
```
SELECT WAR.WAREHOUSE_ID, WAR.WAREHOUSE_NAME, 
       ROUND(SUM(INV.QUANTITY_ON_HAND * PRO_I.LIST_PRICE)) TOTAL_SUM
FROM OE.WAREHOUSES WAR JOIN OE.INVENTORIES INV
     ON WAR.WAREHOUSE_ID = INV.WAREHOUSE_ID JOIN OE.PRODUCT_INFORMATION PRO_I
     ON PRO_I.PRODUCT_ID = INV.PRODUCT_ID
GROUP BY WAR.WAREHOUSE_ID, WAR.WAREHOUSE_NAME
ORDER BY 3 DESC, 2;
