Условие:
--
Используются таблицы стандартной схемы OE.
Одной командой SELECT вывести список покупателей, в информации о которых хранятся два или более телефонных номера.  

В результат вывести пять столбцов:
1. Идентификатор покупателя
2. Имя покупателя
3. Фамилию покупателя
4. Количество хранящихся телефонных номеров покупателя
5. Список хранящихся телефонных номеров покупателя.  

Телефонные номера в списке должны быть:
1. Упорядочены по алфавиту, как строковые данные (по возрастанию)
2. Разделены символами ', ' ("запятая" и "пробел")
3. Перед первым номером телефона не должно быть символов-разделителей
4. После последнего номера телефона символов-разделителей быть не должно.  

Результат упорядочить по идентификатору покупателя по возрастанию.

  
The task:
--
Tables of the standard OE scheme are used.
One SELECT command to display a list of customers in the information about which are stored two or more telephone numbers.  

In the result display five columns:
1. Buyer ID
2. Buyer Name
3. Buyer surname
4. The number of stored customer phone numbers.
5. List of stored telephone numbers of the buyer.  

Phone numbers in the list should be:
1. Sorted alphabetically as string data (ascending)
2. Separated by ',' ("comma" and "space")
3. Before the first phone number there should not be separators
4. There should be no separators after the last phone number.  

Sort the result by customer ID in ascending order.

Решение/ Solution:
--
```SQL
SELECT CUST.CUSTOMER_ID, CUST.CUST_FIRST_NAME, CUST.CUST_LAST_NAME,
       COUNT(PHONE.COLUMN_VALUE) CNT_PHONE, LISTAGG(PHONE.COLUMN_VALUE,', ')
       WITHIN GROUP (ORDER BY PHONE.COLUMN_VALUE) PHONE_LIST
FROM OE.CUSTOMERS CUST NATURAL JOIN TABLE(CUST.PHONE_NUMBERS) PHONE
GROUP BY CUST.CUSTOMER_ID, CUST.CUST_FIRST_NAME, CUST.CUST_LAST_NAME
HAVING COUNT(PHONE.COLUMN_VALUE)>1
ORDER BY CUST.CUSTOMER_ID;
