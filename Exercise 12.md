Условие:
--
Вывести фамилию менеджера, количество его подчиненных всех уровней, а также количество подчиненных всех уровней, работающих в тех же отделах.   

The task:
--
Print the name of the manager, the number of his subordinates of all levels, as well as the number of subordinates of all levels working in the same departments.  

Пример вывода/ Example output:
--
 LAST_NAME     |            	NUM_S             |     	NUM_DEP         
 ----|----|----
King               |       	106	|2
Kochhar            |       	12	|0
De Haan            |       	5	|0
Hunold             |       	4	|4
Greenberg          |       	5	|5
Raphaely           |       	5	|5
Weiss              |       	8	|8
………………		| |


Решение/ Solution:
--
```SQL
SELECT LAST_NAME,
      (SELECT COUNT(*)-1
       FROM EMPLOYEES EMP2
       START WITH EMP1.EMPLOYEE_ID = EMP2.EMPLOYEE_ID
       CONNECT BY PRIOR EMP2.EMPLOYEE_ID = EMP2.MANAGER_ID) NUM_S,
       (SELECT COUNT(CASE
                     WHEN EMP1.DEPARTMENT_ID = EMP3.DEPARTMENT_ID THEN 1
                     ELSE NULL END)-1
       FROM EMPLOYEES EMP3
       START WITH EMP1.EMPLOYEE_ID = EMP3.EMPLOYEE_ID
       CONNECT BY PRIOR EMP3.EMPLOYEE_ID = EMP3.MANAGER_ID) NUM_DEP
FROM EMPLOYEES EMP1
WHERE EMP1.EMPLOYEE_ID IN (SELECT DISTINCT MANAGER_ID FROM EMPLOYEES)
ORDER BY 2 DESC;
