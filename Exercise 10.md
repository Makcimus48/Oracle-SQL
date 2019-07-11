Условие:
--
Вывести статистику по торговым агентам:
1. Имя торгового агента
2. Фамилия торгового агента
3. Сумма выручки сделок, которые сопровождал торговый агент
4. Процент от всех сделок, в которых принимали участие торговые агенты.  


The task:
--
Display statistics on sales agents:
1. Sales Agent Name
2. Last name of sales agent
3. The amount of revenue transactions that accompanied the trading agent
4. Percentage of all transactions in which sales agents participated.  

Решение/ Solution:
--
```
SELECT LAST_NAME, FIRST_NAME, TOTAL_ORD, 
       ROUND(RATIO_TO_REPORT(TOTAL_ORD) OVER () * 100, 2)||'%' PERCENT_S
FROM (SELECT EMP.EMPLOYEE_ID, EMP.LAST_NAME,EMP.FIRST_NAME, 
      ROUND(SUM(ORD.ORDER_TOTAL)) TOTAL_ORD
      FROM EMPLOYEES EMP JOIN OE.ORDERS ORD
      ON EMP.EMPLOYEE_ID = ORD.SALES_REP_ID
      GROUP BY EMP.EMPLOYEE_ID, EMP.LAST_NAME, EMP.FIRST_NAME);
