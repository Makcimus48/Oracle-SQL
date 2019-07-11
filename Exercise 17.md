Условие:
--
В произвольной строке, состоящей из символьных элементов, разделенных запятыми, отсортировать элементы по алфавиту. Одинаковые элементы исключить. Например, символьная строка
> abc,cde,ef,gh,23rtw,mn,test,ss,7rtw,7abc,df,fw,cde,ewe,wwe  

должна быть преобразована к виду:  
> abc,cde,df,ef,ewe, fw,gh,mn,ss,test,wwe,7abc,7rtw,23rtw.  

Символьные элементы, начинающиеся с числа, должны идти после символьных элементов, начинающихся с букв, и должны быть отсортировны сначала по числу, а затем по алфавиту.
  

The task:
--
In an arbitrary string consisting of character elements separated by commas, sort the elements alphabetically. Identical elements to exclude. For example, a character string
> abc, cde, ef, gh, 23rtw, mn, test, ss, 7rtw, 7abc, df, fw, cde, ewe, wwe  

must be converted to:
> abc, cde, df, ef, ewe, fw, gh, mn, ss, test, wwe, 7abc, 7rtw, 23rtw.  

Character elements that begin with a number must appear after character elements that begin with a letter, and must be sorted first by number and then alphabetically. 

Решение/ Solution:
--
```SQL
DEFINE STR = '&Строка' --abc,cde,ef,gh,23rtw,mn,test,ss,7rtw,7abc,df,fw,cde,ewe,wwe
SELECT LISTAGG(TEXT,',') 
WITHIN GROUP
      (ORDER BY CASE WHEN LTRIM(SUBSTR(LOWER(TEXT),1,1),'abcdefghijklmnopqrstuvwxyz') IS NULL THEN TEXT END  NULLS LAST,  
      TO_NUMBER(SUBSTR(TEXT,1,LENGTH(TEXT)-NVL(LENGTH(LTRIM(TEXT,'0123456789')),0))), 
      CASE WHEN (TRIM(LTRIM(TEXT,'0123456789')) IS NOT NULL) THEN TEXT END NULLS FIRST) "Результат"
FROM
(SELECT DISTINCT SUBSTR('&STR', INSTR(','||'&STR'||',',',', 1, LEVEL),
       INSTR(','||'&STR'||',',',', 1, LEVEL+1)-INSTR(','||'&STR'||',',',', 1, LEVEL)-1) TEXT
FROM DUAL
CONNECT BY LEVEL <= LENGTH('&STR')-LENGTH(REPLACE('&STR',',',''))+1);

