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
WITH TAB AS(SELECT 'a,b -> c,d' STR FROM DUAL
            UNION ALL 
            SELECT 'b,d -> a' FROM DUAL
            UNION ALL 
            SELECT 'c -> b,d' FROM DUAL
            UNION ALL 
            SELECT 'd -> a,c,e,f' FROM DUAL
            UNION ALL 
            SELECT 'f -> g' FROM DUAL
            UNION ALL 
            SELECT 'g -> h' FROM DUAL
            UNION ALL 
            SELECT 'd,h -> f' FROM DUAL),
 
   PARS1 AS(SELECT RTRIM(REGEXP_SUBSTR(STR, '(.+ -> )',1),' -> ')S1, 
            LTRIM(REGEXP_SUBSTR(STR, '( .+)',1),' -> ')S2, STR
            FROM TAB)  --SELECT * FROM PARS1;
            ,
            
   PARS2 AS(SELECT DISTINCT REGEXP_SUBSTR(S1,'[^,]+',1, LEVEL) R1, STR FROM PARS1
            CONNECT BY LEVEL <= REGEXP_COUNT(S1,',')+1) --SELECT * FROM PARS2;
            ,            
   PARS3 AS(SELECT DISTINCT REGEXP_SUBSTR(S2,'[^,]+',1, LEVEL) R2, STR FROM PARS1
            CONNECT BY LEVEL <= REGEXP_COUNT(S2,',')+1) --SELECT * FROM PARS3;
            ,              
   A_PARS AS(SELECT STR, R1, R2 FROM PARS2 NATURAL JOIN PARS3
             UNION ALL
             SELECT STR, NULL, R1 FROM PARS2) --SELECT * FROM A_PARS;
             ,
 CONEC_L AS(SELECT DISTINCT R1,R2,SYS_CONNECT_BY_PATH(R2,'->') SYS_CON, CONNECT_BY_ROOT(R2) ROOT, CONNECT_BY_ISCYCLE
            FROM A_PARS
            WHERE CONNECT_BY_ISCYCLE =1
            START WITH R1 IS NULL --IN(SELECT DISTINCT R1 FROM A_PARS)
            CONNECT BY NOCYCLE R1 = PRIOR R2
            ORDER BY 3,2)  --SELECT * FROM CONEC_L;
      
SELECT DISTINCT /*CN.R1, CN.ROOT, CN.R2,*/ LTRIM(CN.SYS_CON||'->'||AP.R2,'->') "CYCLE"
FROM CONEC_L CN JOIN A_PARS AP
ON CN.R2 = AP.R1
WHERE AP.R2 = CN.ROOT;
