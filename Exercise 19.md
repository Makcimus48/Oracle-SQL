Условие:
--
 Определить список последовательностей подчиненности от преподавателей, не имеющих начальника, до преподавателей, не имеющих подчиненных. Результат представить в виде:  

Костыркин-> Викулина-> Студейкин->Соколов (не имеет подчиненных)   

The task:
--
Determine the list of sequences of subordination from teachers who do not have a boss to teachers who do not have subordinates. The result is presented in the form:

Костыркин-> Викулина-> Студейкин->Соколов (no subordinates)

Решение/ Solution:
--
```SQL
SELECT "Последовательность подчинения"
FROM
(SELECT LTRIM(SYS_CONNECT_BY_PATH(ФАМИЛИЯ, '->')|| ' не имеет подчиненных','->') "Последовательность подчинения", CONNECT_BY_ISLEAF LEAF
FROM ПРЕПОДАВАТЕЛЬ
START WITH ПОДЧИНЯЕТСЯ IS NULL
CONNECT BY PRIOR НОМЕР_ПРЕПОДАВАТЕЛЯ = ПОДЧИНЯЕТСЯ)
WHERE LEAF = 1;
