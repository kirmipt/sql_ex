# sql_ex
Решения задач с sql-ex.ru (ниже в этом файле)
<br>Схема БД состоит из четырех таблиц:

* **Product**(maker, model, type)

* **PC**(code, model, speed, ram, hd, cd, price)

* **Laptop**(code, model, speed, ram, hd, price, screen)

* **Printer**(code, model, color, type, price)

Таблица **Product** представляет производителя (maker), номер модели (model) и тип ('PC' - ПК, 'Laptop' - ПК-блокнот или 'Printer' - принтер). Предполагается, что номера моделей в таблице Product уникальны для всех производителей и типов продуктов. 

<br>В таблице **PC** для каждого ПК, однозначно определяемого уникальным кодом – code, указаны модель – model (внешний ключ к таблице Product), скорость - speed (процессора в мегагерцах), объем памяти - ram (в мегабайтах), размер диска - hd (в гигабайтах), скорость считывающего устройства - cd (например, '4x') и цена - price (в долларах). 

<br>Таблица **Laptop** аналогична таблице РС за исключением того, что вместо скорости CD содержит размер экрана -screen (в дюймах). 

<br>В таблице **Printer** для каждой модели принтера указывается, является ли он цветным - color ('y', если цветной), тип принтера - type (лазерный – 'Laser', струйный – 'Jet' или матричный – 'Matrix') и цена - price.


**Cлишком простые задачи, а также задачи, не демонстрирующие новый синтаксис, в этот файл я не записываю**
****
**6. Для каждого производителя, выпускающего ПК-блокноты c объёмом жесткого диска не менее 10 Гбайт, найти скорости таких ПК-блокнотов. Вывод: производитель, скорость.**

```sql
SELECT DISTINCT maker, speed
FROM product p JOIN laptop l 
ON p.model=l.model
WHERE hd>=10
```
****
**Задание: 7 (Serge I: 2002-11-02)
Найдите номера моделей и цены всех имеющихся в продаже продуктов (любого типа) производителя B (латинская буква).**
```sql
SELECT * 
FROM ((SELECT model, price 
   FROM PC)
   UNION
   (SELECT model, price 
   FROM Laptop)
   UNION
   (SELECT model, price 
   FROM Printer)) AS p2
WHERE p2.model IN (SELECT model 
 FROM Product 
 WHERE maker = 'B')
```

<br> 3 WHERE или один WITH ? При больших данных, возможно, WITH быстрее
<br> (это предположение, я не знаю точно)
****
**Задание: 8 (Serge I: 2003-02-03)
Найдите производителя, выпускающего ПК, но не ПК-блокноты.**
```sql
SELECT maker
FROM product 
WHERE type='PC'
EXCEPT 
SELECT maker
FROM product 
WHERE type='Laptop'
```
****
**Задание: 10 (Serge I: 2002-09-23)
Найдите модели принтеров, имеющих самую высокую цену. Вывести: model, price**
```sql
SELECT model, price
FROM printer
WHERE price=(SELECT MAX(price) FROM printer)
```
****
**Рассматривается БД кораблей**, участвовавших во второй мировой войне. Имеются следующие отношения:
* **Classes (class, type, country, numGuns, bore, displacement)**
* **Ships (name, class, launched)**
* **Battles (name, date)**
* **Outcomes (ship, battle, result)**

<br>Корабли в «классах» построены по одному и тому же проекту, и классу присваивается либо имя первого корабля, построенного по данному проекту, либо названию класса дается имя проекта, которое не совпадает ни с одним из кораблей в БД. Корабль, давший название классу, называется головным.

<br>Отношение **Classes** содержит имя класса, тип (bb для боевого (линейного) корабля или bc для боевого крейсера), страну, в которой построен корабль, число главных орудий, калибр орудий (диаметр ствола орудия в дюймах) и водоизмещение ( вес в тоннах).

<br>В отношении **Ships** записаны название корабля, имя его класса и год спуска на воду. 

<br>В отношение **Battles** включены название и дата битвы, в которой участвовали корабли, а в отношении Outcomes – результат участия данного корабля в битве (потоплен-sunk, поврежден - damaged или невредим - OK).
Замечания. 1) 

<br>В отношение **Outcomes** могут входить корабли, отсутствующие в отношении Ships. 2) Потопленный корабль в последующих битвах участия не принимает.
****

****
**Задание: 15 (Serge I: 2003-02-03)
Найдите размеры жестких дисков, совпадающих у двух и более PC. Вывести: HD**
```sql
SELECT hd
FROM pc
GROUP BY hd
HAVING COUNT(*)>1
```
****
**Задание: 16 (Serge I: 2003-02-03)
Найдите пары моделей PC, имеющих одинаковые скорость и RAM. В результате каждая пара указывается только один раз, т.е. (i,j), но не (j,i), Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM.**
```sql
SELECT DISTINCT a.model, b.model, a.speed, a.ram
FROM pc a, pc b
WHERE a.speed=b.speed AND a.ram=b.ram 
AND a.model>b.model
```
****
**Задание: 18 (Serge I: 2003-02-03)
Найдите производителей самых дешевых цветных принтеров. Вывести: maker, price**
```sql
SELECT DISTINCT maker, price
FROM product p JOIN printer pr
ON p.model=pr.model
WHERE color='Y' AND price=(SELECT MIN(price) FROM printer WHERE color='Y')
```
****
**Задание: 20 (Serge I: 2003-02-13)
Найдите производителей, выпускающих по меньшей мере три различных модели ПК. Вывести: Maker, число моделей ПК.**
```sql
SELECT maker, COUNT(model) AS Count_Model
FROM product 
WHERE type='PC'
GROUP BY maker
HAVING COUNT(model)>=3
```
****
**Задание: 23 (Serge I: 2003-02-14)
Найдите производителей, которые производили бы как ПК
со скоростью не менее 750 МГц, так и ПК-блокноты со скоростью не менее 750 МГц.
Вывести: Maker**
```sql
SELECT maker
FROM product p JOIN pc
ON p.model=pc.model
WHERE speed>=750
INTERSECT
SELECT maker
FROM product p JOIN laptop l
ON p.model=l.model
WHERE speed>=750
```
****
**Задание: 24 (Serge I: 2003-02-03)
Перечислите номера моделей любых типов, имеющих самую высокую цену по всей имеющейся в базе данных продукции.**
```sql
WITH p2 AS
(SELECT model, price  
FROM laptop  
UNION
(SELECT model, price 
FROM pc)
UNION
(SELECT model, price 
FROM printer))

SELECT p.model
FROM product p JOIN p2
ON p.model=p2.model
WHERE price=(SELECT MAX(price) FROM p2)
```
****
**Задание: 25 (Serge I: 2003-02-14)
Найдите производителей принтеров, которые производят ПК с наименьшим объемом RAM и с самым быстрым процессором среди всех ПК, имеющих наименьший объем RAM. Вывести: Make**
```sql
SELECT maker 
FROM product p JOIN pc
ON p.model=pc.model
WHERE ram=(SELECT MIN(ram) FROM pc)
AND speed=(SELECT MAX(speed) FROM pc 
    WHERE ram=(SELECT MIN(ram) FROM pc))
INTERSECT
SELECT maker 
FROM product p
WHERE type='Printer'
```
****
**Задание: 26 (Serge I: 2003-02-14)
Найдите среднюю цену ПК и ПК-блокнотов, выпущенных производителем A (латинская буква). Вывести: одна общая средняя цена.**
```sql
WITH b AS
(SELECT price, model FROM pc
UNION ALL
SELECT price, model FROM laptop)

SELECT avg(price) FROM
Product p JOIN
b ON p.model=b.model
WHERE maker='A'
```
****
**Задание: 27 (Serge I: 2003-02-03)
Найдите средний размер диска ПК каждого из тех производителей, которые выпускают и принтеры. Вывести: maker, средний размер HD.**
<br> задачка простая, удалить?
```sql
SELECT maker, AVG(hd)
FROM pc
JOIN product p
ON p.model=pc.model
WHERE maker IN (SELECT maker FROM product WHERE type='Printer')
GROUP BY maker
```
Фирма имеет несколько пунктов приема вторсырья. Каждый пункт получает деньги для их выдачи сдатчикам вторсырья. Сведения о получении денег на пунктах приема записываются в таблицу:
* **Income_o(point, date, inc)**
Первичным ключом является (point, date). При этом в столбец date записывается только дата (без времени), т.е. прием денег (inc) на каждом пункте производится не чаще одного раза в день. Сведения о выдаче денег сдатчикам вторсырья записываются в таблицу:
* **Outcome_o(point, date, out)**
В этой таблице также первичный ключ (point, date) гарантирует отчетность каждого пункта о выданных деньгах (out) не чаще одного раза в день.
В случае, когда приход и расход денег может фиксироваться несколько раз в день, используется другая схема с таблицами, имеющими первичный ключ code:
* **Income(code, point, date, inc)**
* **Outcome(code, point, date, out)**
Здесь также значения столбца date не содержат времени.
****
**Задание: 29 (Serge I: 2003-02-14)
В предположении, что приход и расход денег на каждом пункте приема фиксируется не чаще одного раза в день [т.е. первичный ключ (пункт, дата)], написать запрос с выходными данными (пункт, дата, приход, расход).**
```sql
SELECT i.point, i.date, inc, out
FROM Income_o i
LEFT JOIN Outcome_o o
ON i.point=o.point
   AND i.date=o.date
UNION
SELECT o.point, o.date, inc, out
FROM Income_o i
RIGHT JOIN Outcome_o o
ON i.point=o.point
   AND i.date=o.date
```
****
**Задание: 30 (Serge I: 2003-02-14)
В предположении, что приход и расход денег на каждом пункте приема фиксируется произвольное число раз (первичным ключом в таблицах является столбец code), требуется получить таблицу, в которой каждому пункту за каждую дату выполнения операций будет соответствовать одна строка.
Вывод: point, date, суммарный расход пункта за день (out), суммарный приход пункта за день (inc). Отсутствующие значения считать неопределенными (NULL).**
```sql
SELECT point, date, SUM(out), SUM(inc) 
FROM
(SELECT point, date, null AS out, inc FROM income 
UNION ALL
SELECT point, date, out, null AS inc FROM outcome) x
GROUP BY point, date
```

****
****
```sql

```

****


