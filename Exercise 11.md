Условие:
--
Вывести календарь на текущий месяц (2 цифры месяца месяц (по английски большими буквами) год (четыре цифры) день недели (по-английски маленькими буквами)) все через один пробел. Хвостовых и лидирующих пробелов быть не должно.  

The task:
--
Print the calendar for the current month (2 digits of the month month (in English in capital letters) year (four digits) day of the week (in English in small letters)) all after one space. There should be no tail and leading spaces.  

Решение/ Solution:
--
```SQL
SELECT TO_CHAR(TRUNC(SYSDATE,'MM')+LEVEL - 1,
'DD fmMONTH RRRR day','NLS_DATE_LANGUAGE=AMERICAN') "Календарь"
FROM DUAL
CONNECT BY LEVEL<=TO_CHAR((LAST_DAY(SYSDATE)),'fmDD');
