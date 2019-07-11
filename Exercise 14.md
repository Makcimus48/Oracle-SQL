Условие:
--
Определить ближайший к заданной дате год, когда 29 февраля придется на воскресенье. Решение должно учитывать весь возможный диапазон дат.  

The task:
--
Determine the year nearest to the given date, when February 29 will be on Sunday. The decision should take into account the entire possible range of dates.  

Решение/ Solution:
--
```SQL
DEFINE SD29 = '&Date' -- 12.03.2005 or 28.08.-2458 нет проверки на дурака
WITH DATE_DAY AS(
SELECT  MAX(ABS(ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),12*(LEVEL-1)) - TO_DATE('&SD29','DD.MM.SYYYY'))) D1,
MAX(ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),12*(LEVEL-1))) D2
FROM DUAL
CONNECT BY NOT(
(ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),12*(LEVEL-1))
        -ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),12*(LEVEL-2)) = 366)
        AND (TO_CHAR(ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),12*(LEVEL-2))+59, 'D') = '7'))
UNION ALL
SELECT MAX(ABS(TO_DATE('&SD29','DD.MM.SYYYY') - ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),-12*(LEVEL-1)))) D1,
MIN(ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),-12*(LEVEL-1))) D2
FROM DUAL
CONNECT BY NOT(
(ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),-12*(LEVEL-3))
        -ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),-12*(LEVEL-2)) = 366)
        AND (TO_CHAR(ADD_MONTHS(TRUNC(TO_DATE('&SD29','DD.MM.SYYYY'),'SYYYY'),-12*(LEVEL-2))+59, 'D') = '7')))

--SELECT d1,TO_CHAR( d2, 'DD.MM.SYYYY A.D.') d2 FROM DATE_DAY;

SELECT DISTINCT TO_CHAR(D2,'SYYYY A.D.') "Ближайший год",
TO_CHAR(TO_DATE('&SD29','DD.MM.SYYYY'), 'DD.MM.SYYYY A.D.')"Исходная дата"
FROM DATE_DAY
WHERE D1 = (SELECT MIN(D1) FROM DATE_DAY);

