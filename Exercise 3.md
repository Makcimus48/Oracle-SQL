Условие:
--
Используются таблицы стандартной схемы HR.
Одной командой SELECT вывести список сотрудников компании, имеющих коллег с таким же идентификатором должности и окладом. 
Если данный идентификатор должности и размер оклада имеет один единственный сотрудник, то сведения о нём в результат попадать не должны.
В результат вывести:
1. Идентификатор должности
2. Размер оклада
3. Список фамилий сотрудников, имеющих данный идентификатор должности и данный оклад.  

Фамилии в списке должны быть:
1. Упорядочены по алфавиту (по возрастанию)
2. Разделены символами ', ' ("запятая" и "пробел")
3. Перед первой фамилией не должно быть символов-разделителей
4. После последней фамилии символов-разделителей быть не должно  
   
Результат упорядочить:
1. По размеру оклада (по убыванию)
2. По идентификатору должности (по возрастанию)
  
The task:
--
The tables used are the standard HR schema.
One SELECT team to display a list of company employees who have colleagues with the same job ID and salary.
If this identifier of a position and the size of the salary has a single employee, then information about him in the result should not fall.
The result is sorted in ascending order by columns 1 and 2 listed above. The table name must occur once. When repeating a name, it must be replaced with an empty cell.  

In the result output:
1. Job ID
2. The salary
3. A list of the names of employees with a given job ID and a given salary.

The names in the list should be:
1. Sorted alphabetically (ascending)
2. Separated by ',' ("comma" and "space")
3. There should be no separator characters before the first last name.
4. There should be no separators after the last name  

Result order:
1. By salary (descending)
2. By job ID (ascending)  

Пример вывода/ example output:  
--
JOB_ID | SALARY | EMP_LIST
--- | --- | ---
SA_REP        | 12345  | Banderberg, Maligan
SA_REP        | 9876  | Coreando, McCartney, Silman
FI_ACCOUNT|5432 |Cabrioninni, Finley, Santana, Yarsuoto
***|***|***
ST_CLERK      |2211 | McLaughlin, Philling

Решение/ Solution:
--
```
WITH T AS (SELECT JOB_ID, SALARY, LAST_NAME,
           ROW_NUMBER() OVER (PARTITION BY JOB_ID, SALARY ORDER BY JOB_ID, SALARY) RN
           FROM EMPLOYEES ORDER BY 1,3),
T2 AS (SELECT JOB_ID, SALARY, LTRIM(SYS_CONNECT_BY_PATH(LAST_NAME, ', '), ', ') EMP_LIST, RN, LEVEL LV
       FROM T
       CONNECT BY PRIOR RN = RN-1 AND PRIOR JOB_ID = JOB_ID AND PRIOR SALARY = SALARY) --select * from t2;


SELECT JOB_ID, SALARY, EMP_LIST
FROM T2
WHERE LV > 1 AND
LV =(SELECT MAX(LV) FROM T2 T
     GROUP BY JOB_ID, SALARY
     HAVING T.JOB_ID = T2.JOB_ID AND T.SALARY= T2.SALARY)
ORDER BY SALARY DESC, JOB_ID;
