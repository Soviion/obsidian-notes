| INNER JOIN    | Возвращает только те строки, где есть совпадение в обеих таблицах. Самый строгий и часто используемый.                                                          |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **LEFT JOIN** | Возвращает все строки из левой таблицы (orders) + совпадающие из правой. Если совпадений нет — поля из правой таблицы будут NULL. Самый популярный в аналитике! |
| RIGHT JOIN    | Возвращает все строки из правой таблицы (customers) + совпадающие из левой. Если нет совпадения — поля из левой будут NULL.                                     |
| FULL JOIN     | Возвращает все строки из обеих таблиц. Где нет совпадения — NULL в соответствующих полях.                                                                       |

ORDER BY - сортировка по ASC - возрастанию, DESC - убыванию,
LIMIT - ограничить количество выводимых строк
OFFSET - пропустить первые N строк
IN — значение в списке
BETWEEN — в диапазоне (включительно)
LIKE — поиск по шаблону (% — любые символы, _ — один символ)
IS NULL — проверка на NULL (не = NULL!)
GROUP BY - группирует поле по заданным параметрам.
DISTINCT - вывести уникальные значения

Порядок опeраторов:
SELECT 
FROM 
JOIN 
WHERE 
GROUP BY 
HAVING 
ORDER BY 
LIMIT 

```
SELECT *

FROM flowers
WHERE price_per_unit > 150
  AND is_available = true;

select o.*, c.full_name
from orders o
join customers c on o.customer_id =c.customer_id
where c.full_name = 'Анна Смирнова';              

-- 2 Подсчитать сумму заказов по каждому статусу
select
    status,
    count(*) as status
from orders
group by status;

-- 3 Какое количество букетов заказали суммарно?
select
    count(bouquet_id)
from order_items
where bouquet_id IS NOT null;

-- 4 Для каждого заказа выведите: ID заказа+, дату+, имя клиента+, название товара (букет или цветок)+,
--   количество+, цену за единицу+ и общую сумму позиции+. Отсортируйте по дате заказа (новые сверху).
select
    o.order_id, o.order_date,
    c.full_name,
    f.flower_name, f.price_per_unit,
    b.bouquet_name, b.base_price,
    oi.quantity,
    o.total_amount
from order_items oi
join orders o on oi.order_id  = o.order_id
join customers c on o.customer_id = c.customer_id
left join flowers f on f.flower_id = oi.flower_id
left join bouquets b on b.bouquet_id = oi.bouquet_id
order by o.order_date;

-- 5 Выведите имена клиентов+, их email+, количество заказов+ и общую сумму потраченных средств+. Оставьте только тех
--   у кого больше 1 заказа. Отсортируйте по общей сумме (от большей к меньшей).
select
    c.full_name,
    c.email,
    count(*) as count_orders,
    sum(o.total_amount) as total_price
from orders o
join customers c on c.customer_id = o.customer_id
group by c.full_name, c.email  
having count(*) > 1
order by total_price;

-- 6 Объедините продажи букетов и цветов в один список. Выведите название товара+, тип (Букет/Цветок),
--   общее количество проданных единиц и общую выручку. Отсортируйте по количеству продаж и выведите топ-3.
-- ///лютая шляпа\\\
select
    f.flower_name,
    'Цветок' as type,
    sum(oi.quantity) as countof,
    sum(oi.quantity * f.price_per_unit) as total_price  
from order_items oi
join flowers f on oi.flower_id = f.flower_id
group by f.flower_name

union all
  
select
    b.bouquet_name,
    'Букет' as type,
    sum(oi.quantity) as countof,
    sum(oi.quantity * b.base_price) as total_price  
from order_items oi
join bouquets b on oi.bouquet_id = b.bouquet_id
group by b.bouquet_name
order by countof desc
limit 3;

-- 7 Выведите клиентов, чья общая сумма трат превышает среднюю сумму трат по всем клиентам.
--   Выведите имя, email, город и сумму трат. Отсортируйте по убыванию суммы.
select
    c.full_name,
    c.email,
    c.city,
    sum(o.total_amount) as total_sum
from orders o
join customers c on o.customer_id = c.customer_id
group by c.full_name, c.email, c.city
having sum(o.total_amount) > (select avg(total_amount) from orders)
order by total_sum DESC;

-- 8 Найдите категории цветов, из которых ни один цветок не был заказан ни разу.
--   Выведите ID категории, название и описание.
select *
from categories c
where not exists (
    select * from order_items oi
    join flowers f on f.flower_id  = oi.flower_id
    where f.category_id = c.category_id

)

-- 9 *****Подсчитайте, что приносит больше выручки: продажа готовых букетов или продажа отдельных цветов?
--   Выведите тип товара, общее количество проданных единиц, общую выручку, средний чек за одну позицию и долю в процентах от общей выручки.
```


![[Pasted image 20260602185018.png]]


![[Pasted image 20260602193302.png]]


