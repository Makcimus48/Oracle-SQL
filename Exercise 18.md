Условие:
--
 Имеется таблица с двумя столбцами – дочерняя вершина и родительская вершина. Определить наборы вершин, образующих связанные множества. Например, для таблицы:  
 
 (Входные данные)  
 
 результат должен быть:  
 
 (Выходные данные)

The task:
--
There is a table with two columns - a child node and a parent node. Determine the sets of vertices forming connected sets. For example, for the table:  

(Input data)  

the result should be:  

(Output data)

Входные данные/ Input data:
--
Дочерняя вершина/ Leaf |	Родительская вершина/ Root
----|----
1|	2
2|	4
4|	5
4|	3
7|	6  

Выходные данные/ Output data:
--
|Связанные множества/ Related sets|
|----|
|1,2,3,4,5|
|6,7|



Решение/ Solution:
--
```SQL
WITH TAB AS(SELECT 1 CHILD_N,2 PARENT_N FROM DUAL
             UNION ALL 
             SELECT 2,4 FROM DUAL
             UNION ALL 
             SELECT 4,5 FROM DUAL
             UNION ALL 
             SELECT 4,3 FROM DUAL
             UNION ALL 
             SELECT 6,7 FROM DUAL
             UNION ALL 
             SELECT 6,10 FROM DUAL
             UNION ALL 
             SELECT 9,8 FROM DUAL
             UNION ALL 
             SELECT 10,9 FROM DUAL
             UNION ALL 
             SELECT 5,8 FROM DUAL
             UNION ALL 
             SELECT 15,18 FROM DUAL),

            
  TAB1 AS(SELECT CHILD_N CN, PARENT_N PN 
            FROM TAB
            UNION ALL
            SELECT PARENT_N, NULL
            FROM TAB
            UNION ALL
            SELECT PARENT_N, CHILD_N
            FROM TAB)-- select * from tab1;
            ,             
            
   TAB2 AS(SELECT DISTINCT CONNECT_BY_ROOT(CN) ROOT, CN, PN  FROM TAB1
             WHERE CN IS NOT NULL
             START WITH PN IS NULL
             CONNECT BY NOCYCLE PN =  PRIOR CN)-- select * from tab2 order by 1,2,3;
        
SELECT DISTINCT LISTAGG(replace(CN,',','.'),',') WITHIN GROUP (ORDER BY CN) OVER (PARTITION BY ROOT) "Связанные множества"
FROM TAB2;
