Lab10
================
VY
2022-11-02

``` sql
PRAGMA table_info(actor)
```

| cid | name        | type    | notnull | dflt_value |  pk |
|:----|:------------|:--------|--------:|:-----------|----:|
| 0   | actor_id    | INTEGER |       0 | NA         |   0 |
| 1   | first_name  | TEXT    |       0 | NA         |   0 |
| 2   | last_name   | TEXT    |       0 | NA         |   0 |
| 3   | last_update | TEXT    |       0 | NA         |   0 |

4 records

## Exercise 1

Retrive the actor ID, first name and last name for all actors using the
actor table. Sort by last name and then by first name.

``` sql
SELECT actor_id, first_name, last_name
FROM actor
ORDER by last_name, first_name
```

| actor_id | first_name | last_name |
|---------:|:-----------|:----------|
|       58 | CHRISTIAN  | AKROYD    |
|      182 | DEBBIE     | AKROYD    |
|       92 | KIRSTEN    | AKROYD    |
|      118 | CUBA       | ALLEN     |
|      145 | KIM        | ALLEN     |
|      194 | MERYL      | ALLEN     |
|       76 | ANGELINA   | ASTAIRE   |
|      112 | RUSSELL    | BACALL    |
|      190 | AUDREY     | BAILEY    |
|       67 | JESSICA    | BAILEY    |

Displaying records 1 - 10

Exercise 2 Retrive the actor ID, first name, and last name for actors
whose last name equals ‘WILLIAMS’ or ‘DAVIS’.

``` sql
SELECT actor_id, first_name, last_name
FROM actor
WHERE last_name IN ('WILLIAMS', 'DAVIS')
ORDER by last_name, first_name
```

| actor_id | first_name | last_name |
|---------:|:-----------|:----------|
|        4 | JENNIFER   | DAVIS     |
|      101 | SUSAN      | DAVIS     |
|      110 | SUSAN      | DAVIS     |
|      172 | GROUCHO    | WILLIAMS  |
|      137 | MORGAN     | WILLIAMS  |
|       72 | SEAN       | WILLIAMS  |

6 records

Exercise 3 Write a query against the rental table that returns the IDs
of the customers who rented a film on July 5, 2005 (use the
rental.rental_date column, and you can use the date() function to ignore
the time component). Include a single row for each distinct customer ID.

``` sql
SELECT DISTINCT rental_id
FROM rental
WHERE date(rental_date) = '2005-07-05'
```

| rental_id |
|:----------|
| 3470      |
| 3471      |
| 3472      |
| 3473      |
| 3474      |
| 3475      |
| 3476      |
| 3477      |
| 3478      |
| 3479      |

Displaying records 1 - 10

Exercise 4.1 Construct a query that retrives all rows from the payment
table where the amount is either 1.99, 7.99, 9.99.

``` sql
SELECT *
FROM payment
WHERE amount IN (1.99, 7.99, 9.99)
```

| payment_id | customer_id | staff_id | rental_id | amount | payment_date               |
|-----------:|------------:|---------:|----------:|-------:|:---------------------------|
|      16050 |         269 |        2 |         7 |   1.99 | 2007-01-24 21:40:19.996577 |
|      16056 |         270 |        1 |       193 |   1.99 | 2007-01-26 05:10:14.996577 |
|      16081 |         282 |        2 |        48 |   1.99 | 2007-01-25 04:49:12.996577 |
|      16103 |         294 |        1 |       595 |   1.99 | 2007-01-28 12:28:20.996577 |
|      16133 |         307 |        1 |       614 |   1.99 | 2007-01-28 14:01:54.996577 |
|      16158 |         316 |        1 |      1065 |   1.99 | 2007-01-31 07:23:22.996577 |
|      16160 |         318 |        1 |       224 |   9.99 | 2007-01-26 08:46:53.996577 |
|      16161 |         319 |        1 |        15 |   9.99 | 2007-01-24 23:07:48.996577 |
|      16180 |         330 |        2 |       967 |   7.99 | 2007-01-30 17:40:32.996577 |
|      16206 |         351 |        1 |      1137 |   1.99 | 2007-01-31 17:48:40.996577 |

Displaying records 1 - 10

Exercise 4.2 Construct a query that retrives all rows from the payment
table where the amount is greater then 5

``` sql
SELECT *
FROM payment
WHERE amount > 5
```

| payment_id | customer_id | staff_id | rental_id | amount | payment_date               |
|-----------:|------------:|---------:|----------:|-------:|:---------------------------|
|      16052 |         269 |        2 |       678 |   6.99 | 2007-01-28 21:44:14.996577 |
|      16058 |         271 |        1 |      1096 |   8.99 | 2007-01-31 11:59:15.996577 |
|      16060 |         272 |        1 |       405 |   6.99 | 2007-01-27 12:01:05.996577 |
|      16061 |         272 |        1 |      1041 |   6.99 | 2007-01-31 04:14:49.996577 |
|      16068 |         274 |        1 |       394 |   5.99 | 2007-01-27 09:54:37.996577 |
|      16073 |         276 |        1 |       860 |  10.99 | 2007-01-30 01:13:42.996577 |
|      16074 |         277 |        2 |       308 |   6.99 | 2007-01-26 20:30:05.996577 |
|      16082 |         282 |        2 |       282 |   6.99 | 2007-01-26 17:24:52.996577 |
|      16086 |         284 |        1 |      1145 |   6.99 | 2007-01-31 18:42:11.996577 |
|      16087 |         286 |        2 |        81 |   6.99 | 2007-01-25 10:43:45.996577 |

Displaying records 1 - 10

``` r
# clean up
dbDisconnect(con)
```
