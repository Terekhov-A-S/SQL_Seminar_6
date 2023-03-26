# Базы данных и SQL (семинары)

![pictures MySQL for SQL](https://github.com/Terekhov-A-S/SQL_Seminar_5/blob/main/MySQL.jpg)

## Урок 6. SQL – Транзакции. Временные таблицы, управляющие конструкции, циклы


*Для решения задач используйте базу данных lesson4 (скрипт создания, прикреплен к 4 семинару).*


Для начала создадим и наполним таблицы, используя [**ЭТОТ СКРИПТ**](https://github.com/Terekhov-A-S/SQL_Seminar_4/blob/main/lesson4.sql).


1. Создайте таблицу users_old, аналогичную таблице users. Создайте процедуру, с помощью которой можно переместить любого (одного) пользователя из таблицы users в таблицу users_old (использование транзакции с выбором commit или rollback – обязательно).

```
CREATE OR REPLACE VIEW view_user AS 
SELECT CONCAT(firstname, ' ', lastname, '; ', hometown, '; ', gender) AS 'Пользователи (младше 20 лет)'
FROM users u
JOIN profiles p ON u.id = p.user_id
WHERE TIMESTAMPDIFF(YEAR, birthday, NOW()) < 20
GROUP BY u.id
;
```

*Просмотр представления.*

```
SELECT * FROM view_user;
```

*Удаление представления.*

```
DROP VIEW view_user;
```


2. Создайте хранимую функцию hello(), которая будет возвращать приветствие, в зависимости от текущего времени суток. С 6:00 до 12:00 функция должна возвращать фразу "Доброе утро", с 12:00 до 18:00 функция должна возвращать фразу "Добрый день", с 18:00 до 00:00 — "Добрый вечер", с 00:00 до 6:00 — "Доброй ночи".

```
SELECT 
	DENSE_RANK() OVER (ORDER BY COUNT(from_user_id) DESC) AS 'Место в рейтинге',
	COUNT(from_user_id) AS 'Количество отправленных сообщений',
	CONCAT(firstname, ' ', lastname) AS 'Пользователи'
FROM users u
JOIN messages m ON u.id = m.from_user_id
GROUP BY u.id
;
```


3. (по желанию)* Создайте таблицу logs типа Archive. Пусть при каждом создании записи в таблицах users, communities и messages в таблицу logs помещается время и дата создания записи, название таблицы, идентификатор первичного ключа.

```
SELECT *, 
LAG(created_at, 1, 0) OVER (PARTITION BY TIMESTAMPDIFF(SECOND, created_at, created_at)) AS lag_created_at, -- смещение на 1 и вместо NULL будет 0
LEAD(created_at) OVER (PARTITION BY TIMESTAMPDIFF(SECOND, created_at, created_at)) AS lead_created_at
FROM messages ORDER BY TIMESTAMPDIFF(SECOND, created_at, NOW()) DESC;
```



*Подготовил студент Geek Brains* [**`Терехов Александр`**](https://gb.ru/users/7696463), SQL_Seminar_6
