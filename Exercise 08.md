Условие:
--
Покупатель имеет купюры достоинством A(1), ...,A(n), а продавец - B(1), .. ,B(m). Необходимо найти стоимости товаров Р(k), которые покупатель не может купить, потому что нет возможности точно рассчитаться за  товар с продавцом, хотя денег на покупку этого товара достаточно. Предположить, что купюры могут иметь любое достоинство, измеряемое целым положительным числом. Информация о достоинствах купюр продавца, покупателя и  результат должны быть представлены в виде символьных строк.   

The task:
--
The buyer has denominations of A (1), ..., A (n) denominations, and the seller has B (1), .., B (m). It is necessary to find the value of the goods P (k) that the buyer cannot buy, because there is no way to accurately pay for the goods with the seller, although there is enough money to buy this product. Assume that bills can have any value, measured by a positive integer. Information about the merits of banknotes of the seller, buyer and the result should be presented in the form of character strings.  

Решение/ Solution:
--
```SQL
DEFINE BUYER = '&DATA'
DEFINE SALER = '&DATA'

WITH BUYER_CASH AS(SELECT TO_NUMBER(REGEXP_SUBSTR('&BUYER','\d+',1,LEVEL)) B_CASH
                   FROM DUAL
                   CONNECT BY REGEXP_COUNT('&BUYER','\d+') >= LEVEL),

     SALER_CASH AS(SELECT TO_NUMBER(REGEXP_SUBSTR('&SALER','\d+',1,LEVEL)) S_CASH
                   FROM DUAL
                   CONNECT BY REGEXP_COUNT('&SALER','\d+') >= LEVEL),
                   
     SUM_B_CASH AS(SELECT SUM(B_CASH) SUM_B FROM BUYER_CASH),
     
     ALL_CASH AS(SELECT A_CASH, ROWNUM RN FROM
                 (SELECT B_CASH A_CASH FROM BUYER_CASH
                  UNION ALL
                  SELECT S_CASH FROM SALER_CASH
                  ORDER BY 1)),
     
     COST_CAN_BUY (RN, PRICE) AS(SELECT RN, SUM_B - A_CASH PRICE
                      FROM SUM_B_CASH CROSS JOIN ALL_CASH
                      UNION ALL
                      SELECT AL.RN, COST_CAN_BUY.PRICE - AL.A_CASH
                      FROM COST_CAN_BUY JOIN ALL_CASH AL
                      ON AL.RN>COST_CAN_BUY.RN AND
                         COST_CAN_BUY.PRICE>=AL.A_CASH),
      
     COST_CANT_BUY AS(SELECT LEVEL PRICE
                      FROM SUM_B_CASH
                      CONNECT BY LEVEL < SUM_B
                      MINUS
                      SELECT DISTINCT PRICE FROM COST_CAN_BUY)
                       
SELECT 'Buyer cash: ' NOTATION, LISTAGG(B_CASH,', ') WITHIN GROUP (ORDER BY B_CASH) CASH
FROM BUYER_CASH
UNION ALL
SELECT 'Saler cash: ', LISTAGG(S_CASH,', ') WITHIN GROUP (ORDER BY S_CASH)
FROM SALER_CASH
UNION ALL
SELECT 'Cost of items that can''t be bought: ', LISTAGG(PRICE,', ') WITHIN GROUP (ORDER BY PRICE)
FROM COST_CANT_BUY;

