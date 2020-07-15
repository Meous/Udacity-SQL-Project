/*Query 1 - Selecting movies according to their categories and times rented out*/
SELECT c.name AS category_name,
       COUNT (r.rental_id) AS rental_count
  FROM category AS c
       JOIN film_category AS fc
        ON c.category_id = fc.category_id
       JOIN film AS f
        ON f.film_id = fc.film_id
       JOIN inventory AS i
        ON f.film_id = i.film_id
       JOIN rental AS r
        ON i.inventory_id = r.inventory_id
        WHERE c.name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')
 GROUP BY 1
ORDER BY 2;
--Functions/Methods used : ALIAS, Aggregates, Join



/* Query 2 - Dividing movies into quartiles*/
SELECT f.title Film_Title, 
c.name category_name,
f.rental_duration,
COUNT (r.rental_id) AS rental_count,
NTILE (4) OVER (ORDER BY f.rental_duration) AS standard_quartile
FROM film f
JOIN film_category fc
ON f.film_id = fc.film_id
JOIN category c
ON c.category_id = fc.category_id
JOIN inventory AS i
ON f.film_id = i.film_id
JOIN rental AS r
ON i.inventory_id = r.inventory_id
WHERE c.name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')
GROUP BY 1, 2, 3;
-- Methods used: ALIAS, Join, percentiles, window functions





/* Query 3 - Dividing movies into quartiles*/
SELECT category_name AS name,
       quartiles AS standard_quartile,
       COUNT(category_name)
   FROM (SELECT
       c.name category_name,
       NTILE(4) OVER (ORDER BY f.rental_duration) AS quartiles
   FROM film f
       JOIN film_category fc
	  ON f.film_id = fc.film_id
       JOIN category c
	  ON c.category_id = fc.category_id
       WHERE c.name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')) t1
GROUP BY 1, 2
ORDER BY 1, 2;
/* Methods used: ALIAS, Join, percentiles, window functions, subqueries*/





/*Query 4 - Identifying top 20 paying customers*/
WITH t1 AS (SELECT (first_name || ' ' || last_name) AS name, 
	         c.customer_id, 
	         p.amount, 
	         p.payment_date
	      FROM customer AS c
	         JOIN payment AS p
	             ON c.customer_id = p.customer_id),
	         t2 AS (SELECT t1.customer_id
	      FROM t1
	             GROUP BY 1
	             ORDER BY SUM(t1.amount) DESC
	             LIMIT 20)
	SELECT t1.name,
	       COUNT (*),
	       SUM(t1.amount)
	  FROM t1
	       JOIN t2
	        ON t1.customer_id = t2.customer_id
	 WHERE t1.payment_date BETWEEN '20070101' AND '20071231'
	 GROUP BY 1;
--Functions/Methods: ALIAS, Join, CTE, Aggregates

