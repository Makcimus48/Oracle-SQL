Условие:
--
Вычислить значение суммы
S = 1/1! + 1/2! + ... + 1/k!
Задачу решить двумя способами:
1.	с использованием раздела Model.
2.	Без использования раздела Model.


The task:
--
Calculate the amount of the sum
S = 1/1! + 1/2! + ... + 1 / k!
The problem is solved in two ways:
1. using the Model.
2. Without using the Model.

Решение/ Solution:
--
### Without Model:
```SQL
-- Интересное математическое решение с помощью числа  «е», однако нельзя использовать функцию PL/SQL EXP()
DEFINE ITER = '&DATA'
WITH T AS(SELECT T2.N NUM, ROUND(EXP(SUM(LN(T1.N)))) FACTOR,
1/ROUND(EXP(SUM(LN(T1.N)))) 
FROM 
  ( SELECT LEVEL N
      FROM DUAL
   CONNECT BY LEVEL <= '&ITER') T1,
  ( SELECT LEVEL N
      FROM DUAL
   CONNECT BY LEVEL <= '&ITER') T2
WHERE T1.N<=T2.N
GROUP BY T2.N
ORDER BY T2.N)

SELECT SUM(1/FACTOR) RES
FROM T;
```
### Another without Model:
```SQL
DEFINE ITER = '&DATA'
WITH T(NUM, FACTOR, G) AS 
(SELECT 1 NUM, 1 FACTOR, 1 G FROM dual
UNION ALL
SELECT NUM+1, (NUM+1)*FACTOR, 1/((NUM+1)*FACTOR) FROM T
WHERE NUM <'&ITER')

SELECT SUM(1/FACTOR) RES
FROM T;
```
### With Model:
```SQL
DEFINE K = '&DATA'  
SELECT SUM(ELEMENTS) RES FROM
(SELECT 1/F ELEMENTS, F
       FROM DUAL
       MODEL
       DIMENSION BY (0 D)
       MEASURES (1 F)
       RULES 
       (F[FOR D FROM 1 TO &K-1 INCREMENT 1]=
        F[CV(D)-1]*(CV(D)+1)));
```
