# Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:
фамилия и имя сотрудника из этого магазина;
город нахождения магазина;
количество пользователей, закреплённых в этом магазине.

SELECT 
    CONCAT(staff.first_name, ' ', staff.last_name) AS manager_name,
    city.city AS city_name,
    COUNT(customer.customer_id) AS customer_count
FROM store
JOIN staff ON store.manager_staff_id = staff.staff_id
JOIN address ON store.address_id = address.address_id
JOIN city ON address.city_id = city.city_id
JOIN customer ON store.store_id = customer.store_id
GROUP BY store.store_id, staff.first_name, staff.last_name, city.city
HAVING COUNT(customer.customer_id) > 300;


# Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

SELECT COUNT(*) AS film_count
FROM film
WHERE length > (SELECT AVG(length) FROM film);

# Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

WITH monthly_payments AS (
    SELECT 
        DATE_FORMAT(payment_date, '%Y-%m') AS month,
        SUM(amount) AS total_amount
    FROM payment
    GROUP BY month
),
max_month AS (
    SELECT month
    FROM monthly_payments
    ORDER BY total_amount DESC
    LIMIT 1
)
SELECT 
    max_month.month AS payment_month,
    (SELECT total_amount FROM monthly_payments WHERE month = max_month.month) AS total_payment,
    COUNT(r.rental_id) AS rental_count
FROM max_month
JOIN rental r ON DATE_FORMAT(r.rental_date, '%Y-%m') = max_month.month
GROUP BY max_month.month;
