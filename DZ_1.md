## Особенности уровней изоляции транзакций _'Read committed'_ и _'Repeatable read'_.
## Создаем таблицу _'persons'_ и заполняем:

 > BEGIN;\
 > CREATE TABLE persons (id serial, first_name text, second_name text);\
 > INSERT INTO persons (first_name, second_name) values ('ivan', 'ivanov');\
 > INSERT INTO persons (first_name, second_name) values ('petr', 'petrov');\
 > COMMIT;


## Cмотрим текущий уровень изоляции:
> show transaction isolation level;

### _Результат:_
 > ![read commited](/DZ_1/1.png)
### _Текущий уровень изоляции - 'read committed'._

 ## Начинаем первую и вторую транзакции:
 > BEGIN; 
 
 ## Первая транзакция:

 > insert into persons (first_name, second_name) values ('sergey', 'sergeev');

 ### _Вносим третью запись в таблицу 'persons'._
 
 ## Вторая транзакция:

 > SELECT * FROM persons;

 ### _Результат:_

 > ![](/DZ_1/3.jpg)

 ### _Видим в результате запроса две записи. Третью запись, сделанную в первой транзакции, из второй транзакции не видим. Так как первая транзакция не завершена. Таким образом уровень изоляции 'read commited' позволяет избежать "грязных чтений"._

 ## Первая транзакция:

   > COMMIT;
### _Начинаем первую транзакцию._

 ## Вторая транзакция:

   > SELECT * FROM persons;

 ### _Результат:_

   > ![](/DZ_1/4.jpg)

 ### _Мы видим новую запись, т.к. изменения внесенные первой транзакцией были сохранены командой 'COMMIT'. Теперь эти изменения видны в других транзакциях._

 ## Вторая транзакция:
 > COMMIT;
### _Завершаем вторую транзакцию._
 ## Первая и вторая транзакции:

 > BEGIN;\
 > set transaction isolation level repeatable read;
 ### _Начинаем первую и вторую транзакции. Меняем уровень изоляции на 'repeatable read'._

 ## Первая транзакция:
 > insert into persons (first_name, second_name) values ('sveta', 'svetova');

 ### _Вносим новую запись в таблицу 'persons'._

## Вторая транзакция:

 > SELECT * FROM persons;

### _Результат:_

 > ![](/DZ_1/4.jpg)

 ### _Во второй транзакции изменения сделанные в первой транзакции мы не видим, как и в первом случае._ 

 ## Первая транзакция:

 > COMMIT;
 ### _Завершаем первую транзакцию._

 ## Вторая транзакция:

 > SELECT * FROM persons;
 
 ### _Повторяем запрос._

 ### _Результат:_

 > ![](/DZ_1/4.jpg)

 ### _Результат не изменился, т.к. при текущем уровне изоляции 'repeateble read', мы можем видить только первоначальный набор данных и изменения сделанные в 'своей' (второй) транзакции. А результаты внесенные другими, даже завершенными транзакциями, нет. Таким образом данный уровень изоляции решает проблему "неповторяющихся чтений"._

 ## Вторая транзакция:

 > COMMIT;
### _Завершаем вторую транзакцию._

## Повторяем запрос:

  > SELECT * FROM persons;

### _Результат:_

 > ![](/DZ_1/5.jpg)

 ### _После завершения всех транзакций мы видим все изменения внесенные в таблицу 'persons'._
 
