Условие:
--
Создать запрос для определения информации по сотрудникам, исходя из региона и первых трех цифр телефона:
1.	Регион
2.	Первые три цифры номера телефона
3.	Количество сотрудников
4.	Средняя зарплата
5.	Количество городов
6.	Количество отделов
7.	Список городов через запятую


The task:
--
Create a query to determine information on employees, based on the region and the first three digits of the phone:
1. Region
2. The first three digits of the phone number
3. Number of employees
4. The average salary
5. Number of cities
6. The number of departments
7. List of cities separated by commas

Решение/ Solution:
--
```SQL
WITH TAB1 AS(SELECT SUBSTR(EMP.PHONE_NUMBER,1,3) PHONE, EMP.SALARY, DEP.DEPARTMENT_ID,
             REG.REGION_ID, REG.REGION_NAME, LOC.CITY
             FROM EMPLOYEES EMP LEFT JOIN DEPARTMENTS DEP
             ON EMP.DEPARTMENT_ID = DEP.DEPARTMENT_ID LEFT JOIN LOCATIONS LOC
             ON LOC.LOCATION_ID = DEP.LOCATION_ID LEFT JOIN COUNTRIES CON
             ON LOC.COUNTRY_ID = CON.COUNTRY_ID LEFT JOIN REGIONS REG
             ON REG.REGION_ID = CON.REGION_ID),
     TAB2 AS(SELECT PHONE, REGION_ID, REGION_NAME, 
             LISTAGG(CITY,', ')WITHIN GROUP(ORDER BY 1) CITYS_LIST
             FROM (SELECT DISTINCT PHONE, REGION_NAME, REGION_ID, CITY
                   FROM TAB1)
             GROUP BY REGION_ID, REGION_NAME, PHONE),
     TAB3 AS(SELECT REGION_ID, REGION_NAME, PHONE, COUNT(PHONE) CNT_EMP,
             ROUND(AVG(SALARY),2) AVG_SAL, COUNT(DISTINCT CITY) CNT_CTY,
             COUNT(DISTINCT DEPARTMENT_ID) CNT_DEP
             FROM TAB1
             GROUP BY REGION_ID, REGION_NAME, PHONE)

SELECT NVL(TO_CHAR(T2.REGION_NAME),'No info. Emp have not departments') REGION_NAME, T2.PHONE,
       T3.CNT_EMP, T3.AVG_SAL, T3.CNT_CTY,
       T3.CNT_DEP, NVL(TO_CHAR(T2.CITYS_LIST),'No info. Emp have not departments') CITYS_LIST
       FROM TAB2 T2 LEFT JOIN TAB3 T3
       ON NVL(TO_CHAR(T2.REGION_ID),' ') =NVL(TO_CHAR(T3.REGION_ID),' ') AND
          T2.PHONE = T3.PHONE;
