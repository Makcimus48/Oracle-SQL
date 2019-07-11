Условие:
--
Одной командой SELECT вывести сведения обо всех столбцах таблиц текущей схемы, которые используются во всех внешних ключах таблицы.
В результат вывести пять столбцов: 
1.	Имя таблицы схемы;
2.	Имя ссылочного ограничения целостности (внешнего ключа);
3.	Имена столбцов таблицы, которые входят во внешний ключ (через запятую);
4.	Имя таблицы, на которую ссылается данный внешний ключ;
5.	Имена столбцов таблицы, на которые ссылается данный внешний ключ (через запятую). Порядок следования имен столбцов должен соответствовать порядку столбцов в п.3.  

Результат отсортировать по возрастанию по столбцам 1 и 2, перечисленным выше. Имя таблицы должно встречаться один раз. При повторении имени оно должно заменяться пустой ячейкой.  
  
The task:
--
One SELECT command to display information about all the columns of the tables of the current schema, which are used in all foreign keys of the table.
In the result display five columns:
1. Name of the schema table;
2. The name of the referential integrity constraint (foreign key);
3. The names of the table columns that enter the foreign key (separated by commas);
4. The name of the table to which this foreign key refers;
5. The names of the table columns referenced by this foreign key (comma-separated). The order of the column names should correspond to the order of the columns in 3.  

The result is sorted in ascending order by columns 1 and 2 listed above. The table name must occur once. When repeating a name, it must be replaced with an empty cell.  
  
Решение/ Solution:
--
```
DROP TABLE B;
DROP TABLE A;
  CREATE TABLE A (A1 NUMBER(2), A2 NUMBER(2), A3 NUMBER(2), CONSTRAINT PK_A PRIMARY KEY
  (A1,A2)); 
  CREATE TABLE B (B1 NUMBER(2), B2 NUMBER(2), B3 NUMBER(2), CONSTRAINT FK_B FOREIGN KEY
  (B1,B2) REFERENCES A (A2, A1));

  WITH CRB AS (
  SELECT J.T1 B1,
         J.T2 B2, 
         J.T33 B3,
         J.T4 B4,
         J.T55 B5, ROWNUM RN
  FROM
  (SELECT T1,
         T2, 
         LISTAGG(T3,', ') WITHIN GROUP
        (ORDER BY T3) T33,
         T4,
         LISTAGG(T5,', ') WITHIN GROUP
        (ORDER BY T3) T55
  FROM
  (SELECT UC1.TABLE_NAME T1, UC1.CONSTRAINT_NAME T2, 
         AC1.COLUMN_NAME T3,
         AC2.TABLE_NAME T4,
         AC2.COLUMN_NAME T5
  FROM USER_CONSTRAINTS UC1 JOIN USER_CONS_COLUMNS AC1
       ON UC1.CONSTRAINT_NAME = AC1.CONSTRAINT_NAME 
  JOIN USER_CONS_COLUMNS AC2
       ON UC1.R_CONSTRAINT_NAME = AC2.CONSTRAINT_NAME
  WHERE UC1.CONSTRAINT_TYPE = 'R' AND
  AC1.POSITION = AC2.POSITION
  ORDER BY 1,2,3)
  GROUP BY T1, T2, T4) J)

  SELECT CASE
         WHEN G.B1 = (SELECT G1.B1
         FROM CRB G1
         WHERE G.B1 = G1.B1 AND G1.RN + 1 = G.RN)
         THEN ' '
         ELSE G.B1||':' END "Имя таблицы схемы",
         G.B2 "Имя ссылочного ограничения",
         G.B3 "Имена столбцов,входящих в FK ",
         G.B4  "Имя таблицы, ссылаемой ключом",
         G.B5  "Имена столбцов, ссылаемых PK "
  FROM CRB G;
