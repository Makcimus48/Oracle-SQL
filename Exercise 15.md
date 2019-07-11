Условие:
--
Одной командой SELECT вывести список сотрудников компании, имеющих наименьшие оклад среди сотрудников подразделения, в котором они работают.
Сведения о сотрудниках, для которых неизвестно подразделение компании, к которому они приписаны выводить не нужно.  

В результат вывести:  
1. Идентификатор подразделения компании, к которому приписан сотрудник.
2. Фамилию сотрудника.
3. Оклад, установленный сотруднику.  

В команде SELECT запрещается использовать:
+	Фразы WITH, GROUP BY,HAVING, ORDER BY, CONNECT BY, START WITH,
+	Условия IN, =ANY, =SOME, NOT IN, <> ALL, EXISTS, NOT EXISTS,
+	Подзапросы (subqueries), в том числе подзапросы во фразе FROM,
+	Иерархические запросы (hierarchical queries),
+	Скалярное выражение подзапроса (scalar subquery expression),
+	Агрегатные функции – MIN,MAX, SUM,COUNT, AVG и др.
+	Аналитические функции (analytic functions)


The task:
--
One SELECT team to display a list of company employees with the lowest salary among the employees of the unit in which they work.
Information about employees for whom the division of the company is unknown, to which they are assigned, does not need to be displayed.  

In the result output:
1. The identifier of the division of the company to which the employee is assigned.
2. Name of the employee.
3. Salary set by the employee.  

In the SELECT command it is prohibited to use:
+ Phrases WITH, GROUP BY, HAVING, ORDER BY, CONNECT BY, START WITH,
+ Conditions IN, = ANY, = SOME, NOT IN, <> ALL, EXISTS, NOT EXISTS,
+ Subqueries (subqueries), including subqueries in the FROM clause,
+ hierarchical queries,
+ scalar subquery expression,
+ Aggregate functions - MIN, MAX, SUM, COUNT, AVG, etc.
+ Analytic functions  

Решение/ Solution:
--
```SQL
SELECT DEPARTMENT_ID, LAST_NAME, SALARY
FROM EMPLOYEES
WHERE DEPARTMENT_ID IS NOT NULL
MINUS
SELECT EMP1.DEPARTMENT_ID, EMP1.LAST_NAME, EMP1.SALARY
FROM EMPLOYEES EMP1 JOIN EMPLOYEES EMP2
ON EMP1.DEPARTMENT_ID = EMP2.DEPARTMENT_ID
WHERE EMP1.salary > EMP2.salary;

