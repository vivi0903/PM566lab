lab10
================
Yuwei Wu
2022-11-02

## Setup

``` r
# install.packages(c("RSQLite", "DBI"))

library(RSQLite)
```

    ## Warning: 程辑包'RSQLite'是用R版本4.2.2 来建造的

``` r
library(DBI)
```

    ## Warning: 程辑包'DBI'是用R版本4.2.2 来建造的

``` r
# Initialize a temporary in memory database
con <- dbConnect(SQLite(), ":memory:")

# Download tables
actor <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/actor.csv")
rental <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/rental.csv")
customer <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/customer.csv")
payment <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/payment_p2007_01.csv")

# Copy data.frames to database
dbWriteTable(con, "actor", actor)
dbWriteTable(con, "rental", rental)
dbWriteTable(con, "customer", customer)
dbWriteTable(con, "payment", payment)
dbListTables(con)
```

    ## [1] "actor"    "customer" "payment"  "rental"

TIP: Use can use the following QUERY to see the structure of a table

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

``` r
dbGetQuery(con, "
SELECT actor_id, first_name, last_name
FROM actor
ORDER by last_name, first_name 
LIMIT 15")
```

    ##    actor_id first_name last_name
    ## 1        58  CHRISTIAN    AKROYD
    ## 2       182     DEBBIE    AKROYD
    ## 3        92    KIRSTEN    AKROYD
    ## 4       118       CUBA     ALLEN
    ## 5       145        KIM     ALLEN
    ## 6       194      MERYL     ALLEN
    ## 7        76   ANGELINA   ASTAIRE
    ## 8       112    RUSSELL    BACALL
    ## 9       190     AUDREY    BAILEY
    ## 10       67    JESSICA    BAILEY
    ## 11      115   HARRISON      BALE
    ## 12      187      RENEE      BALL
    ## 13       47      JULIA BARRYMORE
    ## 14      158     VIVIEN  BASINGER
    ## 15      174    MICHAEL    BENING

Try in SQL directly:

``` sql
SELECT   actor_id, first_name, last_name
FROM    actor
ORDER by last_name, first_name
LIMIT 5
```

| actor_id | first_name | last_name |
|---------:|:-----------|:----------|
|       58 | CHRISTIAN  | AKROYD    |
|      182 | DEBBIE     | AKROYD    |
|       92 | KIRSTEN    | AKROYD    |
|      118 | CUBA       | ALLEN     |
|      145 | KIM        | ALLEN     |

5 records

## Exercise 2

Retrive the actor ID, first name, and last name for actors whose last
name equals ‘WILLIAMS’ or ‘DAVIS’.

``` r
dbGetQuery(con, "
SELECT actor_id, first_name, last_name
FROM actor
WHERE last_name IN ('WILLIAMS', 'DAVIS')
ORDER BY last_name
")
```

    ##   actor_id first_name last_name
    ## 1        4   JENNIFER     DAVIS
    ## 2      101      SUSAN     DAVIS
    ## 3      110      SUSAN     DAVIS
    ## 4       72       SEAN  WILLIAMS
    ## 5      137     MORGAN  WILLIAMS
    ## 6      172    GROUCHO  WILLIAMS

## Exercise 3

Write a query against the rental table that returns the IDs of the
customers who rented a film on July 5, 2005 (use the rental.rental_date
column, and you can use the date() function to ignore the time
component). Include a single row for each distinct customer ID.

``` r
dbGetQuery(con, "
SELECT DISTINCT customer_id
FROM rental
WHERE date(rental_date) = '2005-07-05'
GROUP BY customer_id
")
```

    ##    customer_id
    ## 1            8
    ## 2           37
    ## 3           60
    ## 4          111
    ## 5          114
    ## 6          138
    ## 7          142
    ## 8          169
    ## 9          242
    ## 10         295
    ## 11         296
    ## 12         298
    ## 13         322
    ## 14         348
    ## 15         349
    ## 16         369
    ## 17         382
    ## 18         397
    ## 19         421
    ## 20         476
    ## 21         490
    ## 22         520
    ## 23         536
    ## 24         553
    ## 25         565
    ## 26         586
    ## 27         594

## Exercise 4

### Exercise 4.1

Construct a query that retrives all rows from the payment table where
the amount is either 1.99, 7.99, 9.99.

``` r
dbGetQuery(con, "
SELECT *
FROM payment
WHERE amount IN (1.99, 7.99, 9.99)
LIMIT 10
")
```

    ##    payment_id customer_id staff_id rental_id amount               payment_date
    ## 1       16050         269        2         7   1.99 2007-01-24 21:40:19.996577
    ## 2       16056         270        1       193   1.99 2007-01-26 05:10:14.996577
    ## 3       16081         282        2        48   1.99 2007-01-25 04:49:12.996577
    ## 4       16103         294        1       595   1.99 2007-01-28 12:28:20.996577
    ## 5       16133         307        1       614   1.99 2007-01-28 14:01:54.996577
    ## 6       16158         316        1      1065   1.99 2007-01-31 07:23:22.996577
    ## 7       16160         318        1       224   9.99 2007-01-26 08:46:53.996577
    ## 8       16161         319        1        15   9.99 2007-01-24 23:07:48.996577
    ## 9       16180         330        2       967   7.99 2007-01-30 17:40:32.996577
    ## 10      16206         351        1      1137   1.99 2007-01-31 17:48:40.996577

Exercise 4.2 Construct a query that retrives all rows from the payment
table where the amount is greater then 5

``` r
dbGetQuery(con, "
SELECT *
FROM payment
WHERE amount > 5
LIMIT 10
")
```

    ##    payment_id customer_id staff_id rental_id amount               payment_date
    ## 1       16052         269        2       678   6.99 2007-01-28 21:44:14.996577
    ## 2       16058         271        1      1096   8.99 2007-01-31 11:59:15.996577
    ## 3       16060         272        1       405   6.99 2007-01-27 12:01:05.996577
    ## 4       16061         272        1      1041   6.99 2007-01-31 04:14:49.996577
    ## 5       16068         274        1       394   5.99 2007-01-27 09:54:37.996577
    ## 6       16073         276        1       860  10.99 2007-01-30 01:13:42.996577
    ## 7       16074         277        2       308   6.99 2007-01-26 20:30:05.996577
    ## 8       16082         282        2       282   6.99 2007-01-26 17:24:52.996577
    ## 9       16086         284        1      1145   6.99 2007-01-31 18:42:11.996577
    ## 10      16087         286        2        81   6.99 2007-01-25 10:43:45.996577

### Exercise 4.3

Construct a query that retrieves all rows from the payment table where
the amount is greater then 5 and less then 8

``` r
dbGetQuery(con, "
SELECT *
FROM payment
WHERE amount > 5 AND amount > 8
GROUP BY payment_id
LIMIT 10
")
```

    ##    payment_id customer_id staff_id rental_id amount               payment_date
    ## 1       16058         271        1      1096   8.99 2007-01-31 11:59:15.996577
    ## 2       16073         276        1       860  10.99 2007-01-30 01:13:42.996577
    ## 3       16102         293        2      1034   8.99 2007-01-31 03:22:06.996577
    ## 4       16113         299        2       606   8.99 2007-01-28 13:17:05.996577
    ## 5       16125         304        1       135  10.99 2007-01-25 20:27:24.996577
    ## 6       16154         315        1       537   8.99 2007-01-28 04:49:21.996577
    ## 7       16157         316        1       644   8.99 2007-01-28 17:27:38.996577
    ## 8       16160         318        1       224   9.99 2007-01-26 08:46:53.996577
    ## 9       16161         319        1        15   9.99 2007-01-24 23:07:48.996577
    ## 10      16187         334        1       431   8.99 2007-01-27 14:59:31.996577

## Exercise 5

Retrieve all the payment IDs and their amount from the customers whose
last name is ‘DAVIS’.

``` r
dbGetQuery(con, "
SELECT payment_id, amount
FROM payment AS p INNER JOIN customer AS c   ON p.customer_id = c.customer_id
WHERE c.last_name = 'DAVIS'
")
```

    ##   payment_id amount
    ## 1      16685   4.99
    ## 2      16686   2.99
    ## 3      16687   0.99

## Exercise 6

### Exercise 6.1

Use COUNT(\*) to count the number of rows in rental

``` r
dbGetQuery(con, "
SELECT COUNT(*)
FROM rental
")
```

    ##   COUNT(*)
    ## 1    16044

### Exercise 6.2

Use COUNT(\*) and GROUP BY to count the number of rentals for each
customer_id

``` r
dbGetQuery(con, "
SELECT customer_id, COUNT(*) AS 'N Rentals'
FROM rental
GROUP BY customer_id
LIMIT 8
")
```

    ##   customer_id N Rentals
    ## 1           1        32
    ## 2           2        27
    ## 3           3        26
    ## 4           4        22
    ## 5           5        38
    ## 6           6        28
    ## 7           7        33
    ## 8           8        24

## Exercise 6.3

Repeat the previous query and sort by the count in descending order

``` r
dbGetQuery(con, "
SELECT customer_id, COUNT(*) AS 'N Rentals'
FROM rental
GROUP BY customer_id
ORDER BY `N Rentals` DESC
/*ORDER BY - `N Rentals`*/
LIMIT 8
")
```

    ##   customer_id N Rentals
    ## 1         148        46
    ## 2         526        45
    ## 3         236        42
    ## 4         144        42
    ## 5          75        41
    ## 6         469        40
    ## 7         197        40
    ## 8         468        39

## Exercise 6.4

Repeat the previous query but use HAVING to only keep the groups with 40
or more.

``` r
dbGetQuery(con, "
SELECT customer_id, COUNT(*) AS N
FROM rental
GROUP BY customer_id
HAVING N >= 40 
ORDER BY N DESC
")
```

    ##   customer_id  N
    ## 1         148 46
    ## 2         526 45
    ## 3         236 42
    ## 4         144 42
    ## 5          75 41
    ## 6         469 40
    ## 7         197 40

# Exercise 7

The following query calculates a number of summary statistics for the
payment table using MAX, MIN, AVG and SUM

``` r
dbGetQuery(con, "
SELECT
  MAX(amount) AS max,
  MIN(amount) AS min,
  AVG(amount) AS average,
  SUM(amount) as sum
FROM payment
")
```

    ##     max  min  average     sum
    ## 1 11.99 0.99 4.169775 4824.43

## Exercise 7.1

Modify the above query to do those calculations for each customer_id

``` r
dbGetQuery(con, "
SELECT
  customer_id,
  COUNT(*) AS N,
  MAX(amount) AS max,
  MIN(amount) AS min,
  AVG(amount) AS average,
  SUM(amount) as sum
FROM payment
GROUP BY customer_id
LIMIT 5
")
```

    ##   customer_id N  max  min  average  sum
    ## 1           1 2 2.99 0.99 1.990000 3.98
    ## 2           2 1 4.99 4.99 4.990000 4.99
    ## 3           3 2 2.99 1.99 2.490000 4.98
    ## 4           5 3 6.99 0.99 3.323333 9.97
    ## 5           6 3 4.99 0.99 2.990000 8.97

## Exercise 7.2

Modify the above query to only keep the customer_ids that have more then
5 payments

``` r
dbGetQuery(con, "
SELECT
  customer_id,
  COUNT(*) AS N,
  MAX(amount) AS max,
  MIN(amount) AS min,
  AVG(amount) AS average,
  SUM(amount) as sum
FROM payment
GROUP BY customer_id
HAVING N > 5
")
```

    ##    customer_id N  max  min  average   sum
    ## 1           19 6 9.99 0.99 4.490000 26.94
    ## 2           53 6 9.99 0.99 4.490000 26.94
    ## 3          109 7 7.99 0.99 3.990000 27.93
    ## 4          161 6 5.99 0.99 2.990000 17.94
    ## 5          197 8 3.99 0.99 2.615000 20.92
    ## 6          207 6 6.99 0.99 2.990000 17.94
    ## 7          239 6 7.99 2.99 5.656667 33.94
    ## 8          245 6 8.99 0.99 4.823333 28.94
    ## 9          251 6 4.99 1.99 3.323333 19.94
    ## 10         269 6 6.99 0.99 3.156667 18.94
    ## 11         274 6 5.99 2.99 4.156667 24.94
    ## 12         371 6 6.99 0.99 4.323333 25.94
    ## 13         506 7 8.99 0.99 4.132857 28.93
    ## 14         596 6 6.99 0.99 3.823333 22.94

## Cleanup

Run the following chunk to disconnect from the connection.

``` r
# clean up
dbDisconnect(con)
```
