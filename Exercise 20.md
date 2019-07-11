Условие:
--
Для произвольного целого числа определить числа, полученные перестановками  цифр в числе  и имеющие максимальные суммы абсолютных разностей между соседними цифрами. Например, для числа 1239 результат должен быть:  

3192  

2913  

Суммы абсолютных разностей равны:  

|3-1| + |1-9| + |9-2| = 17  

|2-9| + |9-1| + |1-3| = 17  

Решить с помощью раздела Model.


The task:
--
For an arbitrary integer number, determine the numbers obtained by permutations of digits in a number and having the maximum sums of absolute differences between adjacent digits. For example, for the number 1239, the result should be:

3192

2913

The sums of absolute differences are equal to: 

|3-1| + |1-9| + |9-2| = 17  

|2-9| + |9-1| + |1-3| = 17  

Solve using the Model.

Решение/ Solution:
--
```SQL
DEFINE STR='&data' –1457
WITH NUMBERS AS (SELECT REGEXP_SUBSTR('&STR', '\d',1,LEVEL) NUM, ROWNUM RN
            FROM DUAL CONNECT BY LEVEL <=LENGTH('&STR')) --SELECT * FROM NUMBERS;
            ,
    VARYBLE AS (SELECT DISTINCT REPLACE(SYS_CONNECT_BY_PATH(NUM,'|'),'|') NUM1
                FROM NUMBERS WHERE CONNECT_BY_ISLEAF = 1
                CONNECT BY NOCYCLE PRIOR RN != RN AND LEVEL <=LENGTH('&STR')) --SELECT * FROM VARYBLE;
                ,
    ABS1 AS (SELECT ABSSUM, ABS, NUM1 FROM VARYBLE
            MODEL
            PARTITION BY (NUM1)
            DIMENSION BY (0 i)
            MEASURES(0 ABS, 0 ABSSUM)
            RULES(ABS[FOR i FROM 1 TO LENGTH('&STR')-1 INCREMENT 1]=
            ABS(SUBSTR(CV(NUM1),CV(i),1)-SUBSTR(CV(NUM1),CV(i)+1,1)),
            ABSSUM[0]=0,ABSSUM[FOR i FROM 1 TO LENGTH('&STR')-1 INCREMENT 1]=
            ABSSUM[CV(i)-1]+ABS[CV(i)]))
SELECT NUM1 ,ABSSUM AS RES FROM ABS1
WHERE ABSSUM = (SELECT MAX(ABSSUM) FROM ABS1); 
