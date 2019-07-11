Условие:
--
Вывести название отдела и фамилии сотрудников с минимальным окладом по департаменту (при одинаковом минимальном окладе фамилии должны выводиться через запятую). Не учитывать людей, не привязанных ни к одному департаменту.  

The task:
--
Print the department name and the names of the employees with the minimum salary for the department (with the same minimum salary, the names must be comma-separated). Do not take into account people who are not tied to any department.  

Решение/ Solution:
--
```SQL
--Находим мин оклады по отделу
WITH DOP AS (SELECT MIN(SALARY) M_S, DEPARTMENT_ID D_I
              FROM EMPLOYEES
              GROUP BY DEPARTMENT_ID)

SELECT A1 "Название отдела",
LISTAGG(A2, ', ') WITHIN GROUP
(ORDER BY A1) "Сотру-ки с мин sal по отделу"
FROM
(SELECT A1,A2
FROM
(SELECT 
        --Выписываем названия отделов
        (SELECT D.DEPARTMENT_NAME
        FROM DEPARTMENTS D
        WHERE E.DEPARTMENT_ID = D.DEPARTMENT_ID) A1,
        --Выписываем только те фамилии сотрудников у которых зарплата минимальна по отделу
        --у остальных ставиться null
       (SELECT E1.LAST_NAME
        FROM EMPLOYEES E1 JOIN DOP L
        ON E1.DEPARTMENT_ID = L.D_I
        WHERE E.EMPLOYEE_ID = E1.EMPLOYEE_ID AND
              E1.SALARY = L.M_S) A2
FROM EMPLOYEES E)
WHERE A2 IS NOT NULL)
GROUP BY A1;
