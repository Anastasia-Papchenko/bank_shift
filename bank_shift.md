# Выполнение тестового задания по курсу разработки

## Подготовила: Папченко Анастасия Вячеславовна

Ссылки на задания:
<!-- TOC --><a name="00"></a>

- [0](#0) 
- [1](#1)
- [2](#2)
- [3](#3)
- [4](#4) 
- [5](#5)
- [6](#6)
- [7](#7)
- [8](#8)
- [9](#9)
- [10](#10)
- [11](#11)

<!-- TOC --><a name="0"></a>
**0. Подготовка к выполнению задания. Необходимо создать базу данных. Для выполнения тестового задания использовалась бд - ```PostgreSQL```.**

[Вверх](#00)

```sql
CREATE DATABASE bank_shift;
\c bank_shift;
```
<!-- TOC --><a name="1"></a>
**1. Подготовьте DDL-скрипты создания объектов для приведённой модели: создание таблиц, первичных, уникальных, внешних ключей и т.д.**

[Вверх](#00)

```Таблица «Клиенты»```
```sql
CREATE TABLE clients (
    id             integer PRIMARY KEY,
    name           varchar(100) NOT NULL,
    place_of_birth varchar(100),
    date_of_birth  date,
    address        varchar(100),
    passport       varchar(100)
);
```
```Таблица «Тарифы»```
```sql
CREATE TABLE tarifs (
    id   integer PRIMARY KEY,
    name varchar(100) NOT NULL,
    cost numeric(10,2) NOT NULL
);
```
```Таблица «Типы продуктов»```
```sql
CREATE TABLE product_type (
    id         integer PRIMARY KEY,
    name       varchar(100) NOT NULL, 
    begin_date date NOT NULL,
    end_date   date,
    tarif_ref  integer REFERENCES tarifs(id)
);
```
```Таблица «Продукты»```
```sql
CREATE TABLE products (
    id              integer PRIMARY KEY,
    product_type_id integer NOT NULL REFERENCES product_type(id),
    name            varchar(100) NOT NULL,
    client_ref      integer NOT NULL REFERENCES clients(id),
    open_date       date NOT NULL,
    close_date      date
);
```
```Таблица «Счета»```
```sql
CREATE TABLE accounts (
    id          integer PRIMARY KEY,
    name        varchar(100) NOT NULL,
    saldo       numeric(15,2) NOT NULL DEFAULT 0,
    client_ref  integer NOT NULL REFERENCES clients(id),
    open_date   date NOT NULL,
    close_date  date,
    product_ref integer NOT NULL REFERENCES products(id),
    acc_num     varchar(25) NOT NULL UNIQUE
);
```
```Таблица «Операции по счетам»```
```sql
CREATE TABLE records (
    id        integer PRIMARY KEY,
    dt        smallint NOT NULL CHECK (dt IN (0,1)),  
    sum       numeric(15,2) NOT NULL,
    acc_ref   integer NOT NULL REFERENCES accounts(id),
    oper_date date NOT NULL
);
```

<!-- TOC --><a name="2"></a>
**2. Заполните таблицы примерами из приложенного скрипта, вы найдете скрипт ниже после всех заданий.**  

[Вверх](#00)

```Тарифы```
```sql
INSERT INTO tarifs VALUES
  (1, 'Тариф за выдачу кредита', 10),
  (2, 'Тариф за открытие счета', 10),
  (3, 'Тариф за обслуживание карты', 10); 
```
```Типы продуктов```
```sql
INSERT INTO product_type VALUES
  (1, 'КРЕДИТ',  to_date('01.01.2018','DD.MM.YYYY'), NULL, 1),
  (2, 'ДЕПОЗИТ', to_date('01.01.2018','DD.MM.YYYY'), NULL, 2),
  (3, 'КАРТА', to_date('01.01.2018','DD.MM.YYYY'), NULL, 3);
``` 
```Клиенты```
```sql
INSERT INTO clients VALUES 
   (1, 'Сидоров Иван Петрович', 'Россия, Московская облать, г. Пушкин', to_date('01.01.2001','DD.MM.YYYY'), 'Россия, Московская облать, г. Пушкин, ул. Грибоедова, д. 5', '2222 555555, выдан ОВД г. Пушкин, 10.01.2015'),
   (2, 'Иванов Петр Сидорович', 'Россия, Московская облать, г. Клин', to_date('01.01.2001','DD.MM.YYYY'), 'Россия, Московская облать, г. Клин, ул. Мясникова, д. 3', '4444 666666, выдан ОВД г. Клин, 10.01.2015'),
   (3, 'Петров Сиодр Иванович', 'Россия, Московская облать, г. Балашиха', to_date('01.01.2001','DD.MM.YYYY'), 'Россия, Московская облать, г. Балашиха, ул. Пушкина, д. 7', '4444 666666, выдан ОВД г. Клин, 10.01.2015'); 
```
```Продукты```
```sql
INSERT INTO products VALUES
  (1, 1, 'Кредитный договор с Сидоровым И.П.', 1, to_date('01.06.2015','DD.MM.YYYY'), null),
  (2, 2, 'Депозитный договор с Сидоровым И.П.', 2, to_date('01.08.2017','DD.MM.YYYY'), null),
  (3, 3, 'Карточный договор с Сидоровым И.П.', 3, to_date('01.08.2017','DD.MM.YYYY'), null); 
```
```Счета```
```sql
INSERT INTO accounts VALUES
  (1, 'Кредитный счет для Сидорова И.П.', -2000, 1, to_date('01.06.2015','DD.MM.YYYY'), null, 1, '45502810401020000022'),
  (2, 'Депозитный счет для Сидорова И.П.', 6000, 2, to_date('01.08.2017','DD.MM.YYYY'), null, 2, '42301810400000000001'), 
  (3, 'Карточный счет для Сидорова И.П.', 8000, 3, to_date('01.08.2017','DD.MM.YYYY'), null, 3, '40817810700000000001'); 
```
```Проводки```
```sql
INSERT INTO records VALUES
  (1, 1, 5000, 1, to_date('01.06.2015','DD.MM.YYYY')),
  (2, 0, 1000, 1, to_date('01.07.2015','DD.MM.YYYY')),  
  (3, 0, 2000, 1, to_date('01.08.2015','DD.MM.YYYY')),    
  (4, 0, 3000, 1, to_date('01.09.2015','DD.MM.YYYY')),
  (5, 1, 5000, 1, to_date('01.10.2015','DD.MM.YYYY')),
  (6, 0, 3000, 1, to_date('01.10.2015','DD.MM.YYYY')),
  (7, 0, 10000, 2, to_date('01.08.2017','DD.MM.YYYY')),
  (8, 1, 1000, 2, to_date('05.08.2017','DD.MM.YYYY')), 
  (9, 1, 2000, 2, to_date('21.09.2017','DD.MM.YYYY')),
  (10, 1, 5000, 2, to_date('24.10.2017','DD.MM.YYYY')),
  (11, 0, 6000, 2, to_date('26.11.2017','DD.MM.YYYY')),
  (12, 0, 120000, 3, to_date('08.09.2017','DD.MM.YYYY')),
  (13, 1, 1000, 3, to_date('05.10.2017','DD.MM.YYYY')),
  (14, 1, 2000, 3, to_date('21.10.2017','DD.MM.YYYY')), 
  (15, 1, 5000, 3, to_date('24.10.2017','DD.MM.YYYY'));
```

<!-- TOC --><a name="3"></a>
**3. Подготовьте скрипты заполнения таблиц тестовыми данными, достаточными для выполнения заданий ниже.**

[Вверх](#00)

```sql
INSERT INTO clients VALUES 
   (4, 'Габец Евгения Владимировна', 'Россия, г. Арсеньев', to_date('10.05.1979','DD.MM.YYYY'), 'Россия, г. Арсеньев, ул. Ломоносова, д. 1', '1234 567890'),
   (5, 'Сукнева Наталья Федоровна', 'Россия, г. Арсеньев', to_date('03.03.1951','DD.MM.YYYY'), 'Россия, г. Арсеньев, ул. 25 лет Арсеньеву, д. 10', '1234 567890'),
   (6, 'Петров Алексей Иванович', 'Россия, г. Москва', to_date('15.05.1980','DD.MM.YYYY'), 'Москва, ул. Ленина, 10', '1234 111111');
```
```sql
INSERT INTO products VALUES 
   (4, 1, 'Кредитный договор с Габец Е.В.', 4, to_date('15.01.2023','DD.MM.YYYY'), NULL),
   (5, 2, 'Депозитный договор с Сукнева Н.Ф.', 5, to_date('20.02.2023','DD.MM.YYYY'), NULL),
   (6, 3, 'Карточный договор с Габец Е.В.', 4, to_date('10.03.2023','DD.MM.YYYY'), NULL),
   (7, 2, 'Депозитный договор с Петровым А.И.', 6, to_date('15.01.2024','DD.MM.YYYY'), NULL);
```
```sql
INSERT INTO accounts VALUES 
   (4, 'Кредитный счет Габец Е.В.', -50000, 4, to_date('15.01.2023','DD.MM.YYYY'), NULL, 4, '45502810401020000033'),
   (5, 'Депозитный счет Сукнева Н.Ф.', 100000, 5, to_date('20.02.2023','DD.MM.YYYY'), NULL, 5, '42301810400000000002'),
   (6, 'Карточный счет Габец Е.В.', 50000, 4, to_date('10.03.2023','DD.MM.YYYY'), NULL, 6, '40817810700000000002'),
   (7, 'Депозитный счет Петрова А.И.', 50000, 6, to_date('15.01.2024','DD.MM.YYYY'), NULL, 7, '42301810400000000003'),
   (8, 'Счет с расхождением', 1000, 4, to_date('01.01.2024','DD.MM.YYYY'), NULL, 6, '40817810700000000003'),
   (10, 'Неактивный счет', 1000, 6, to_date('01.12.2023','DD.MM.YYYY'), NULL, 7, '40817810700000000004');
```
```sql
INSERT INTO records VALUES 
   (16, 1, 5000, 4, CURRENT_DATE - INTERVAL '5 days'),
   (17, 0, 10000, 5, CURRENT_DATE - INTERVAL '10 days'),
   (18, 1, 2000, 6, CURRENT_DATE - INTERVAL '15 days'),
   (19, 0, 15000, 5, CURRENT_DATE - INTERVAL '20 days'),
   (20, 1, 3000, 4, CURRENT_DATE - INTERVAL '25 days'),
   (21, 0, 50000, 7, to_date('15.01.2024','DD.MM.YYYY')),
   (22, 0, 5000, 8, to_date('01.01.2024','DD.MM.YYYY')),
   (23, 1, 2000, 8, to_date('02.01.2024','DD.MM.YYYY')),
   (27, 0, 1000, 10, to_date('01.12.2023','DD.MM.YYYY')),
   (28, 1, 200000, 4, to_date('01.02.2023','DD.MM.YYYY')),
   (29, 0, 300000, 5, to_date('01.02.2023','DD.MM.YYYY'));  
```

<!-- TOC --><a name="4"></a>
**4. Сформируйте отчет, который содержит все счета, относящиеся к продуктам типа ДЕПОЗИТ, принадлежащих клиентам, у которых нет открытых продуктов типа КРЕДИТ.**

[Вверх](#00)

```sql
SELECT 
    c.id as client_id,
    c.name as client_name,
    a.acc_num as account_number,
    a.name as account_name,
    a.saldo as current_balance,
    p.open_date as product_open_date
FROM accounts a
JOIN products p ON a.product_ref = p.id
JOIN product_type pt ON p.product_type_id = pt.id
JOIN clients c ON a.client_ref = c.id
WHERE pt.name = 'ДЕПОЗИТ'
AND c.id NOT IN (
    SELECT DISTINCT c2.id
    FROM clients c2
    JOIN products p2 ON c2.id = p2.client_ref
    JOIN product_type pt2 ON p2.product_type_id = pt2.id
    WHERE pt2.name = 'КРЕДИТ'
    AND p2.close_date IS NULL
)
AND p.close_date IS NULL
ORDER BY c.name, a.open_date;
```


```Вывод:```

| client_id | client_name              | account_number       | account_name                        | current_balance | product_open_date |
|-----------|--------------------------|----------------------|--------------------------------------|-----------------|-------------------|
| 2         | Иванов Петр Сидорович    | 42301810400000000001 | Депозитный счет для Сидорова И.П.   | 6000.00         | 2017-08-01        |
| 6         | Петров Алексей Иванович  | 40817810700000000004 | Неактивный счет                      | 1000.00         | 2024-01-15        |
| 6         | Петров Алексей Иванович  | 42301810400000000003 | Депозитный счет Петрова А.И.         | 50000.00        | 2024-01-15        |
| 5         | Сукнева Наталья Федоровна| 42301810400000000002 | Депозитный счет Сукнева Н.Ф.         | 100000.00       | 2023-02-20        |

<!-- TOC --><a name="5"></a>
**5. Сформируйте выборку, которая содержит средние движения по счетам в рамках одного дня в разрезе типа продукта.**

[Вверх](#00)

```sql
SELECT
    pt.name AS product_type,
    t.operation_date,
    ROUND(AVG(t.daily_movement), 2) AS avg_daily_movement_per_account
FROM (
    SELECT
        p.product_type_id,
        DATE(r.oper_date) AS operation_date,
        a.id AS account_id,
        SUM(
            CASE 
                WHEN r.dt = 0 THEN r.sum   
                ELSE -r.sum                
            END
        ) AS daily_movement
    FROM records r
    JOIN accounts a ON r.acc_ref = a.id
    JOIN products p ON a.product_ref = p.id
    GROUP BY
        p.product_type_id,
        DATE(r.oper_date),
        a.id
) t
JOIN product_type pt ON t.product_type_id = pt.id
GROUP BY
    pt.name,
    t.operation_date
ORDER BY
    pt.name,
    t.operation_date;
```

```Вывод:```
| product_type | operation_date | avg_daily_movement_per_account |
|--------------|----------------|--------------------------------|
| ДЕПОЗИТ      | 2017-08-01     | 10000.00                       |
| ДЕПОЗИТ      | 2017-08-05     | -1000.00                       |
| ДЕПОЗИТ      | 2017-09-21     | -2000.00                       |
| ДЕПОЗИТ      | 2017-10-24     | -5000.00                       |
| ДЕПОЗИТ      | 2017-11-26     | 6000.00                        |
| ДЕПОЗИТ      | 2023-02-01     | 300000.00                      |
| ДЕПОЗИТ      | 2023-12-01     | 1000.00                        |
| ДЕПОЗИТ      | 2024-01-15     | 50000.00                       |
| ДЕПОЗИТ      | 2025-11-08     | 15000.00                       |
| ДЕПОЗИТ      | 2025-11-18     | 10000.00                       |
| КАРТА        | 2017-09-08     | 120000.00                      |
| КАРТА        | 2017-10-05     | -1000.00                       |
| КАРТА        | 2017-10-21     | -2000.00                       |
| КАРТА        | 2017-10-24     | -5000.00                       |
| КАРТА        | 2024-01-01     | 5000.00                        |
| КАРТА        | 2024-01-02     | -2000.00                       |
| КАРТА        | 2025-11-13     | -2000.00                       |
| КРЕДИТ       | 2015-06-01     | -5000.00                       |
| КРЕДИТ       | 2015-07-01     | 1000.00                        |
| КРЕДИТ       | 2015-08-01     | 2000.00                        |
| КРЕДИТ       | 2015-09-01     | 3000.00                        |
| КРЕДИТ       | 2015-10-01     | -2000.00                       |
| КРЕДИТ       | 2023-02-01     | -200000.00                     |
| КРЕДИТ       | 2025-11-03     | -3000.00                       |
| КРЕДИТ       | 2025-11-23     | -5000.00                       |

<!-- TOC --><a name="6"></a>
**6. Сформируйте выборку, в которую попадут клиенты, у которых были операции по счетам за прошедший месяц от текущей даты. Выведите клиента и сумму операций за день в разрезе даты.**

(now: 2025-11-28)

[Вверх](#00)

```sql
SELECT 
    c.id AS client_id,
    c.name AS client_name,
    DATE(r.oper_date) AS operation_date,
    SUM(
        CASE 
            WHEN r.dt = 0 THEN r.sum    
            ELSE -r.sum                 
        END
    ) AS daily_operations_amount
FROM records r
JOIN accounts a ON r.acc_ref = a.id
JOIN clients c ON a.client_ref = c.id
WHERE r.oper_date >= CURRENT_DATE - INTERVAL '1 month'
  AND r.oper_date <= CURRENT_DATE
GROUP BY 
    c.id, 
    c.name, 
    DATE(r.oper_date)
ORDER BY 
    operation_date DESC, 
    c.name;
```
```Вывод:```
| client_id | client_name               | operation_date | daily_operations_amount |
|-----------|---------------------------|----------------|-------------------------|
| 4         | Габец Евгения Владимировна| 2025-11-23     | -5000.00                |
| 5         | Сукнева Наталья Федоровна | 2025-11-18     | 10000.00                |
| 4         | Габец Евгения Владимировна| 2025-11-13     | -2000.00                |
| 5         | Сукнева Наталья Федоровна | 2025-11-08     | 15000.00                |
| 4         | Габец Евгения Владимировна| 2025-11-03     | -3000.00                |

<!-- TOC --><a name="7"></a>
**7. В результате сбоя в базе данных разъехалась информация между остатками и операциями по счетам. Напишите нормализацию (процедуру выравнивающую данные), которая найдет такие счета и восстановит остатки по счету.**

[Вверх](#00)

```1.Находим счета с расхождениями```
```sql  
SELECT 
    a.id as account_id,
    a.acc_num as account_number,
    a.name as account_name,
    a.saldo as current_saldo,
    SUM(CASE 
        WHEN r.dt = 0 THEN r.sum  
        ELSE -r.sum              
    END) as calculated_saldo,
    a.saldo - SUM(CASE 
        WHEN r.dt = 0 THEN r.sum
        ELSE -r.sum
    END) as discrepancy
FROM accounts a
JOIN records r ON a.id = r.acc_ref
GROUP BY a.id, a.acc_num, a.name, a.saldo
HAVING a.saldo != SUM(CASE 
    WHEN r.dt = 0 THEN r.sum
    ELSE -r.sum
END);
```
| account_id | account_number       | account_name                       | current_saldo | calculated_saldo | discrepancy |
|------------|----------------------|------------------------------------|---------------|------------------|-------------|
| 4          | 45502810401020000033 | Кредитный счет Габец Е.В.         | -50000.00     | -208000.00       | 158000.00   |
| 6          | 40817810700000000002 | Карточный счет Габец Е.В.         | 50000.00      | -2000.00         | 52000.00    |
| 5          | 42301810400000000002 | Депозитный счет Сукнева Н.Ф.      | 100000.00     | 325000.00        | -225000.00  |
| 3          | 40817810700000000001 | Карточный счет для Сидорова И.П.  | 8000.00       | 112000.00        | -104000.00  |
| 8          | 40817810700000000003 | Счет с расхождением               | 1000.00       | 3000.00          | -2000.00    |
| 1          | 45502810401020000022 | Кредитный счет для Сидорова И.П.  | -2000.00      | -1000.00         | -1000.00    |
| 2          | 42301810400000000001 | Депозитный счет для Сидорова И.П. | 6000.00       | 8000.00          | -2000.00    |

```2.Функция нормализации```

*Функция normalize_account_balances находит все счета, у которых сальдо в таблице accounts не сходится с суммой операций в records, исправляет это сальдо на пересчитанное и возвращает список всех исправленных счетов с их старым и новым балансом*
```sql
CREATE OR REPLACE FUNCTION normalize_account_balances()
RETURNS void
LANGUAGE plpgsql
AS $$
BEGIN
    WITH corrected_balances AS (
        SELECT 
            a.id,
            SUM(
                CASE 
                    WHEN r.dt = 0 THEN r.sum
                    ELSE -r.sum
                END
            ) AS new_saldo
        FROM accounts a
        JOIN records r ON a.id = r.acc_ref
        GROUP BY a.id
        HAVING a.saldo <> SUM(
            CASE 
                WHEN r.dt = 0 THEN r.sum
                ELSE -r.sum
            END
        )
    )
    UPDATE accounts a
    SET saldo = cb.new_saldo
    FROM corrected_balances cb
    WHERE a.id = cb.id;
END;
$$;
```
```3.Вызываем функцию```
```sql
SELECT normalize_account_balances();
```
```4.Проверяем результат с помощью скрипта из шага 1```
| account_id | account_number | account_name | current_saldo | calculated_saldo | discrepancy |
|------------|----------------|--------------|---------------|------------------|-------------|
(0 строк)

<!-- TOC --><a name="8"></a>
**8. Сформируйте выборку, которая вернет информацию о клиентах, которые полностью погасили кредит, но при этом не закрыли продукт.**

[Вверх](#00)

```sql
SELECT 
    c.id as client_id,
    c.name as client_name,
    p.id as product_id,
    p.name as product_name,
    a.id as account_id,
    a.acc_num as account_number,
    a.saldo as current_balance,
    SUM(CASE 
        WHEN r.dt = 1 THEN r.sum  
        ELSE -r.sum               
    END) as net_credit_balance
FROM clients c
JOIN products p ON c.id = p.client_ref
JOIN product_type pt ON p.product_type_id = pt.id
JOIN accounts a ON p.id = a.product_ref
JOIN records r ON a.id = r.acc_ref
WHERE pt.name = 'КРЕДИТ'
AND p.close_date IS NULL
GROUP BY c.id, c.name, p.id, p.name, a.id, a.acc_num, a.saldo
HAVING SUM(CASE 
    WHEN r.dt = 1 THEN r.sum
    ELSE -r.sum
END) >= 0 
ORDER BY c.name;
```
```Вывод:```
| client_id | client_name               | product_id | product_name                      | account_id | account_number       | current_balance | net_credit_balance |
|-----------|---------------------------|------------|-----------------------------------|------------|----------------------|-----------------|--------------------|
| 4         | Габец Евгения Владимировна| 4          | Кредитный договор с Габец Е.В.    | 4          | 45502810401020000033 | -208000.00      | 208000.00          |
| 1         | Сидоров Иван Петрович     | 1          | Кредитный договор с Сидоровым И.П.| 1          | 45502810401020000022 | -1000.00        | 1000.00            |

<!-- TOC --><a name="9"></a>
**9. Закройте продукты (установите дату закрытия равную текущей) типа «КРЕДИТ», у которых произошло полное погашение, но при этом не было повторной выдачи.**

(now: 2025-11-28)

[Вверх](#00)

```1.Находим продукты типа КРЕДИТ, которые можно закрыть```
```sql
SELECT 
    p.id as product_id,
    p.name as product_name,
    c.name as client_name,
    a.saldo as current_balance,
    SUM(CASE 
        WHEN r.dt = 1 THEN r.sum
        ELSE -r.sum
    END) as calculated_balance
FROM products p
JOIN product_type pt ON p.product_type_id = pt.id
JOIN clients c ON p.client_ref = c.id
JOIN accounts a ON p.id = a.product_ref
JOIN records r ON a.id = r.acc_ref
WHERE pt.name = 'КРЕДИТ'
AND p.close_date IS NULL
GROUP BY p.id, p.name, c.name, a.saldo
HAVING SUM(CASE 
    WHEN r.dt = 1 THEN r.sum
    ELSE -r.sum
END) >= 0;
```
```Вывод:```
| product_id | product_name                      | client_name               | current_balance | calculated_balance |
|------------|-----------------------------------|---------------------------|-----------------|--------------------|
| 1          | Кредитный договор с Сидоровым И.П.| Сидоров Иван Петрович     | -1000.00        | 1000.00            |
| 4          | Кредитный договор с Габец Е.В.    | Габец Евгения Владимировна| -208000.00      | 208000.00          |

```2.Закрываем найденные продукты сегодняшней датой```
```sql
UPDATE products 
SET close_date = CURRENT_DATE
WHERE id IN (
    SELECT p.id
    FROM products p
    JOIN product_type pt ON p.product_type_id = pt.id
    JOIN accounts a ON p.id = a.product_ref
    JOIN records r ON a.id = r.acc_ref
    WHERE pt.name = 'КРЕДИТ'
    AND p.close_date IS NULL
    GROUP BY p.id
    HAVING SUM(CASE 
        WHEN r.dt = 1 THEN r.sum
        ELSE -r.sum
    END) >= 0
);
```
```3.Проверяем результат с помощью скрипта из шага 1 или по скрипту ниже```
```sql
SELECT id, name, close_date 
FROM products 
WHERE close_date = CURRENT_DATE;
```
```Вывод:```
| id | name                              | close_date |
|----|-----------------------------------|------------|
| 1  | Кредитный договор с Сидоровым И.П.| 2025-11-28 |
| 4  | Кредитный договор с Габец Е.В.    | 2025-11-28 |

<!-- TOC --><a name="10"></a>
**10. Закройте возможность открытия (установите дату окончания действия) для типов продуктов, по счетам продуктов которых, не было движений более одного месяца.**

[Вверх](#00)

```1.Находим, то что будем закрывать```
```sql
SELECT 
    p.id,
    p.name,
    p.product_type_id,
    p.open_date,
    p.close_date,
    MAX(r.oper_date) AS last_operation_date
FROM products p
LEFT JOIN accounts a ON p.id = a.product_ref
LEFT JOIN records r ON a.id = r.acc_ref
WHERE p.close_date IS NULL 
GROUP BY 
    p.id, p.name, p.product_type_id, p.open_date, p.close_date
HAVING 
    MAX(r.oper_date) IS NULL
    OR MAX(r.oper_date) < CURRENT_DATE - INTERVAL '1 month'
ORDER BY last_operation_date NULLS FIRST, p.id;
```
```Вывод:```
| id | name                               | product_type_id | open_date  | close_date | last_operation_date |
|----|------------------------------------|-----------------|------------|------------|---------------------|
| 3  | Карточный договор с Сидоровым И.П. | 3               | 2017-08-01 |            | 2017-10-24          |
| 2  | Депозитный договор с Сидоровым И.П.| 2               | 2017-08-01 |            | 2017-11-26          |
| 7  | Депозитный договор с Петровым А.И. | 2               | 2024-01-15 |            | 2024-01-15          |

```2.Закрываем```
```sql
UPDATE products p
SET close_date = CURRENT_DATE
WHERE p.close_date IS NULL
  AND NOT EXISTS (
      SELECT 1
      FROM accounts a
      JOIN records r ON a.id = r.acc_ref
      WHERE a.product_ref = p.id
        AND r.oper_date >= CURRENT_DATE - INTERVAL '1 month'
  );
```
```3.Проверяем результат с помощью скрипта из шага 1```
```Вывод:```
| id | name | product_type_id | open_date | close_date | last_operation_date |
|----|------|-----------------|-----------|------------|---------------------|
(0 строк)

<!-- TOC --><a name="11"></a>
**11. В модель данных добавьте сумму договора по продукту. Заполните поле для всех продуктов суммой максимальной дебетовой операции по счету для продукта типа «КРЕДИТ», и суммой максимальной кредитовой операции по счету продукта для продукта типа «ДЕПОЗИТ» или «КАРТА».**

[Вверх](#00)

```1.Добавляем колонку contract_amount в таблицу products```
```sql
ALTER TABLE products
ADD COLUMN contract_amount numeric(15,2);
```
```2.Заполняем поле contract_amount согласно условию```
```sql
UPDATE products p
SET contract_amount = src.contract_amount
FROM (
    SELECT 
        p.id AS product_id,
        CASE 
            WHEN pt.name = 'КРЕДИТ' THEN
                MAX(CASE WHEN r.dt = 1 THEN r.sum END)         
            WHEN pt.name IN ('ДЕПОЗИТ', 'КАРТА') THEN
                MAX(CASE WHEN r.dt = 0 THEN r.sum END)         
            ELSE
                NULL
        END AS contract_amount
    FROM products p
    JOIN product_type pt ON p.product_type_id = pt.id
    LEFT JOIN accounts a ON p.id = a.product_ref
    LEFT JOIN records r ON a.id = r.acc_ref
    GROUP BY p.id, pt.name
) AS src
WHERE p.id = src.product_id;
```
```3.Проверяем результат```
```sql
SELECT 
    p.id,
    p.name AS product_name,
    pt.name AS product_type,
    p.contract_amount
FROM products p
JOIN product_type pt ON p.product_type_id = pt.id
ORDER BY pt.name, p.id;
```
```Вывод:```
| id | product_name                       | product_type | contract_amount |
|----|------------------------------------|--------------|-----------------|
| 2  | Депозитный договор с Сидоровым И.П.| ДЕПОЗИТ      | 10000.00        |
| 5  | Депозитный договор с Сукнева Н.Ф.  | ДЕПОЗИТ      | 300000.00       |
| 7  | Депозитный договор с Петровым А.И. | ДЕПОЗИТ      | 50000.00        |
| 3  | Карточный договор с Сидоровым И.П. | КАРТА        | 120000.00       |
| 6  | Карточный договор с Габец Е.В.     | КАРТА        | 5000.00         |
| 1  | Кредитный договор с Сидоровым И.П. | КРЕДИТ       | 5000.00         |
| 4  | Кредитный договор с Габец Е.В.     | КРЕДИТ       | 200000.00       |
