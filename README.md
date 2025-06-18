# Introduction to PostgreSQL
PostgreSQL also known as Postgres, is a free and open-source relational database management system (RDBMS) emphasizing extensibility and SQL compliance. PostgreSQL features transactions with atomicity, consistency, isolation, durability (ACID) properties, automatically updatable views, materialized views, triggers, foreign keys, and stored procedures.

# Installation
## Install PostgreSQL on Debian/Ubuntu
Automated repository configuration: 
```
sudo apt install -y postgresql-common
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
```
To manually configure the Apt repository, follow these steps:
```
# Import the repository signing key:
sudo apt install curl ca-certificates
sudo install -d /usr/share/postgresql-common/pgdg
sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc

# Create the repository configuration file:
. /etc/os-release
sudo sh -c "echo 'deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $VERSION_CODENAME-pgdg main' > /etc/apt/sources.list.d/pgdg.list"

# Update the package lists:
sudo apt update

# Install the latest version of PostgreSQL:
# If you want a specific version, use 'postgresql-17' or similar instead of 'postgresql'
sudo apt -y install postgresql
```

# Creating a Database
To create a new database, in this example named mydb, you use the following command:
``` sql
postgres@jihan-pc:~$ createdb test
postgres@jihan-pc:~$ psql --list
                                                 List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges   
-----------+----------+----------+-------------+-------------+------------+-----------------+-----------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | 
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
           |          |          |             |             |            |                 | postgres=CTc/postgres
 test      | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | 
(4 rows)
```
If you are connected to an existing database, you can use CREATE DATABASE (SQL Command):
```
CREATE DATABASE test;
```

# Connect to Database
Once you have created a database, you can access it by running the PostgreSQL interactive terminal program, called psql, which allows you to interactively enter, edit, and execute SQL commands.
```
psql -h localhost -U postgres -d test
```
# Delete a database
If you do not want to use your database anymore you can remove it. For example, if you are the owner (creator) of the database test, you can destroy it using the following command:
```
dropdb test
```
If you are connected to an existing database, you can use DROP DATABASE (SQL Command):
```
DROP DATABASE test;
```

# Create Table
You can create a new table by specifying the table name, along with all column names and their types:
``` sql
CREATE TABLE person (
    id INT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    gender VARCHAR(6),
    date_of_birth DATE
);
```

## Create Table with constraints
``` sql
CREATE TABLE person (
    id BIGSERIAL NOT NULL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    gender VARCHAR(6) NOT NULL,
    date_of_birth DATE NOT NULL
);
```
List of table now:
``` sql
test=# \d
              List of relations
 Schema |     Name      |   Type   |  Owner   
--------+---------------+----------+----------
 public | person        | table    | postgres
 public | person_id_seq | sequence | postgres
(2 rows)
```

# Insert records into table
The INSERT statement is used to populate a table with rows:
``` sql
INSERT INTO person (
    first_name,
    last_name,
    gender,
    date_of_birth)
VALUES ('John', 'Dell', 'MALE', DATE '1998-05-06');
```

# SELECT
Fetch all data from table:
``` sql
test=# SELECT * FROM person;
 id | first_name | last_name | gender | date_of_birth 
----+------------+-----------+--------+---------------
  1 | John       | Dell      | MALE   | 1998-05-06
  2 | Anne       | Becca     | FEMALE | 1998-04-03
(2 rows)
```


# ORDER BY
The ORDER BY keyword is used to sort the result-set in ascending or descending order.
``` sql
SELECT * FROM person ORDER BY first_name;
```
Output
```
  id  |  first_name  |      last_name      |               email               |   gender    | date_of_birth |          country_of_birth          
------+--------------+---------------------+-----------------------------------+-------------+---------------+------------------------------------
  769 | Abba         | Sellers             | asellerslc@hexun.com              | Male        | 1985-10-31    | Thailand
  161 | Abbi         | Godfray             | agodfray4g@skyrock.com            | Female      | 2012-03-09    | Indonesia
  163 | Abbie        | Corballis           | acorballis4i@aboutads.info        | Male        | 2005-02-13    | United States
  261 | Abbot        | Blackstone          | ablackstone78@tinypic.com         | Male        | 2011-05-13    | China
  726 | Abey         | Healing             |                                   | Male        | 1998-03-27    | Philippines
  829 | Abram        | Ciccetti            | aciccettin0@histats.com           | Male        | 2004-12-21    | China
  659 | Abramo       | Bein                |                                   | Male        | 2006-12-11    | Canada
  356 | Ad           | Chamberlayne        | achamberlayne9v@squidoo.com       | Agender     | 1980-12-26    | Albania
```

# DISTINCT
The SELECT DISTINCT statement is used to return only distinct (different) values.
``` sql
SELECT DISTINCT last_name FROM person; 
```
```
      last_name      
---------------------
 Huguet
 Amori
 Kenryd
 Anthoine
 Culy
 Kernar
 Rizzini
 Kielty
 Aylmer
 Springtorpe
 Goulstone
 Stocker
 Dimelow
 de Quesne
 O'Hartnedy
```
Inside a table, a column often contains many duplicate values; and sometimes you only want to list the different (distinct) values.

# WHERE
The WHERE clause is used to filter records. It is used to extract only those records that fulfill a specified condition.
``` sql
SELECT * FROM person WHERE first_name = 'Abey';
```
```
 id  | first_name | last_name | email | gender | date_of_birth | country_of_birth 
-----+------------+-----------+-------+--------+---------------+------------------
 726 | Abey       | Healing   |       | Male   | 1998-03-27    | Philippines
(1 row)
```

# LIMIT OFFSET FETCH
The OFFSET-FETCH clause in SQL is a powerful tool used for pagination, allowing users to retrieve a subset of rows from a result set. It is especially useful when dealing with large datasets, enabling smooth navigation through data by skipping a certain number of rows and fetching only the required ones.

``` sql
test=# SELECT * from person LIMIT 5;
 id | first_name | last_name |          email          |   gender   | date_of_birth | country_of_birth 
----+------------+-----------+-------------------------+------------+---------------+------------------
  1 | Amalie     | Dunsmuir  | adunsmuir0@rediff.com   | Female     | 2022-07-12    | Brazil
  2 | Cyndia     | End       | cend1@sciencedirect.com | Female     | 2011-11-24    | Swaziland
  3 | Bonnie     | Girke     | bgirke2@paypal.com      | Polygender | 1983-02-02    | Philippines
  4 | Danya      | Lapsley   | dlapsley3@hhs.gov       | Female     | 2022-03-25    | China
  5 | Maryl      | Prozillo  |                         | Female     | 2015-09-12    | Armenia
(5 rows)

```
``` sql
test=# SELECT * from person WHERE country_of_birth = 'Russia' OFFSET 3 LIMIT 5;
 id | first_name | last_name |             email             | gender | date_of_birth | country_of_birth 
----+------------+-----------+-------------------------------+--------+---------------+------------------
 37 | Gnni       | Bound     | gbound10@goodreads.com        | Female | 1995-10-11    | Russia
 53 | Bessie     | Josephi   |                               | Female | 2007-05-02    | Russia
 62 | Celle      | McNeice   | cmcneice1p@joomla.org         | Female | 1989-10-29    | Russia
 63 | Hillary    | Woodrup   | hwoodrup1q@chicagotribune.com | Female | 2002-03-06    | Russia
 67 | Brigitta   | Stoving   | bstoving1u@purevolume.com     | Female | 2024-11-05    | Russia
(5 rows)

```
``` sql
test=# SELECT * from person OFFSET 5 FETCH FIRST 5 ROW ONLY;
 id | first_name | last_name |           email            |   gender    | date_of_birth | country_of_birth 
----+------------+-----------+----------------------------+-------------+---------------+------------------
  6 | Tyson      | Becraft   | tbecraft5@i2i.jp           | Male        | 1999-07-29    | Russia
  7 | Tomi       | Southall  | tsouthall6@sphinn.com      | Agender     | 2013-05-04    | Chad
  8 | Cris       | Hawtrey   | chawtrey7@upenn.edu        | Male        | 2019-08-20    | Indonesia
  9 | Kial       | Allport   | kallport8@bandcamp.com     | Female      | 1988-11-03    | Indonesia
 10 | Iris       | Pittet    | ipittet9@deliciousdays.com | Genderqueer | 2023-03-18    | El Salvador
(5 rows)
```

# IN
The IN operator allows you to specify multiple values in a WHERE clause. The IN operator is a shorthand for multiple OR conditions.
``` sql
test=# SELECT * FROM person WHERE country_of_birth IN ('Chad', 'Greece');
 id  | first_name  | last_name  |            email            | gender  | date_of_birth | country_of_birth 
-----+-------------+------------+-----------------------------+---------+---------------+------------------
   7 | Tomi        | Southall   | tsouthall6@sphinn.com       | Agender | 2013-05-04    | Chad
  14 | Berne       | Hayne      | bhayned@noaa.gov            | Male    | 2007-12-12    | Greece
  46 | Diane       | Cully      | dcully19@nih.gov            | Female  | 1982-06-02    | Greece
  49 | Bidget      | Bridgwater | bbridgwater1c@wordpress.org | Female  | 2004-08-14    | Greece
  66 | Panchito    | Burney     |                             | Male    | 1983-06-02    | Greece
 169 | Nananne     | Arundel    | narundel4o@geocities.jp     | Female  | 1986-10-09    | Greece
 248 | Vernon      | De Mitris  |                             | Male    | 1988-08-13    | Greece
(more)
```


# LIKE 
The LIKE operator is used in a WHERE clause to search for a specified pattern in a column. There are two wildcards often used in conjunction with the LIKE operator:

* The percent sign % represents zero, one, or multiple characters
* The underscore sign _ represents one, single character

``` sql
test=# SELECT * FROM person WHERE email LIKE '%fema%';
 id  | first_name | last_name  |         email          | gender | date_of_birth | country_of_birth 
-----+------------+------------+------------------------+--------+---------------+------------------
 107 | Bronnie    | Fobidge    | bfobidge2y@fema.gov    | Male   | 1994-09-18    | Indonesia
 258 | Edita      | Sandercock | esandercock75@fema.gov | Female | 2022-07-21    | Indonesia
(2 rows)
```
``` sql
test=# SELECT * FROM person WHERE email LIKE '_____e@%';
 id  | first_name | last_name |      email      | gender | date_of_birth | country_of_birth 
-----+------------+-----------+-----------------+--------+---------------+------------------
 267 | Sandra     | Hug       | shug7e@webs.com | Female | 1988-02-14    | Kazakhstan
 699 | Hamish     | Dog       | hdogje@msu.edu  | Male   | 1987-05-03    | China
(2 rows)
```

# AND
The WHERE clause can contain one or many AND operators. The AND operator is used to filter records based on more than one condition.
``` sql
test=# SELECT * FROM person WHERE gender = 'Male' AND first_name LIKE 'Ja%';
 id  | first_name | last_name |            email             | gender | date_of_birth |   country_of_birth    
-----+------------+-----------+------------------------------+--------+---------------+-----------------------
  24 | Javier     | Espy      | jespyn@sbwire.com            | Male   | 2001-06-21    | Liechtenstein
 246 | Jarrett    | Kielty    | jkielty6t@google.com         | Male   | 2017-04-11    | Russia
 283 | Jarib      | Thain     |                              | Male   | 2000-01-10    | Indonesia
 414 | Jasen      | Spancock  | jspancockbh@statcounter.com  | Male   | 1987-03-22    | China
 489 | Jacobo     | Mehaffey  | jmehaffeydk@domainmarket.com | Male   | 2019-01-27    | France
 508 | Jacobo     | Defries   | jdefriese3@msn.com           | Male   | 1994-10-02    | Paraguay
 673 | Javier     | Kuhnert   | jkuhnertio@webs.com          | Male   | 1984-05-16    | Poland
 738 | Jammal     | Dymock    | jdymockkh@e-recht24.de       | Male   | 1986-07-25    | Palestinian Territory
 984 | Jamison    | Raincin   | jraincinrb@amazon.co.uk      | Male   | 1995-07-24    | Nigeria
(9 rows)
```

# GROUP BY
The GROUP BY statement groups rows that have the same values into summary rows.
``` sql
test=# SELECT country_of_birth, count(*) FROM person GROUP BY country_of_birth ORDER BY country_of_birth;
          country_of_birth          | count 
------------------------------------+-------
 Afghanistan                        |     5
 Albania                            |     2
 Angola                             |     2
 Argentina                          |    10
 Armenia                            |     4
 Australia                          |     2
 Azerbaijan                         |     4
 Bahamas                            |     1
 Belarus                            |     3
 Bolivia                            |     5
 Bonaire, Saint Eustatius and Saba  |     1
 Bosnia and Herzegovina             |     6
 Brazil                             |    47
(more)
```

## GROUP BY HAVING
``` sql
test=# SELECT gender, COUNT(*) FROM person GROUP BY gender HAVING COUNT(*) > 10;
   gender    | count 
-------------+-------
 Bigender    |    22
 Genderfluid |    14
 Male        |   441
 Non-binary  |    17
 Polygender  |    16
 Female      |   467
 Agender     |    14
(7 rows)
```

# MIN, MAX, AVG
The MIN() function returns the smallest value of the selected column. The MAX() function returns the largest value of the selected column. The AVG() function returns the smallest value of the selected column. 
``` sql
test=# SELECT MIN(price), MAX(price), AVG(price) FROM car;
   min   |   max    |        avg         
---------+----------+--------------------
 1482.51 | 99819.73 | 51736.597160000000
(1 row)
```

# SUM
The SUM() function returns the total sum of a numeric column.
``` sql
test=# SELECT SUM(price) AS total_price FROM car;
 total_price 
-------------
 51736597.16
(1 row)
```

# Arithmetic Operators Basics
``` sql
test=# SELECT make, model, price,
    price + 1000 AS price_plus_1000,
    price * 1.1 AS price_with_10_percent_increase
FROM car;
     make      |          model          |  price   | price_plus_1000 | price_with_10_percent_increase 
---------------+-------------------------+----------+-----------------+--------------------------------
 Alfa Romeo    | Spider                  | 82165.49 |        83165.49 |                      90382.039
 Toyota        | Tacoma                  | 57462.80 |        58462.80 |                      63209.080
 Lexus         | LX                      | 80665.18 |        81665.18 |                      88731.698
 Audi          | A6                      | 67269.82 |        68269.82 |                      73996.802
 Chrysler      | Concorde                | 84949.49 |        85949.49 |                      93444.439
 Buick         | Skylark                 | 12019.46 |        13019.46 |                      13221.406
 Mitsubishi    | Diamante                | 42435.56 |        43435.56 |                      46679.116
 Pontiac       | Firebird Formula        | 14905.84 |        15905.84 |                      16396.424
 BMW           | 7 Series                | 15042.85 |        16042.85 |                      16547.135
 Aston Martin  | V8 Vantage S            | 34653.66 |        35653.66 |                      38119.026
 Dodge         | Ram Van B250            | 87826.49 |        88826.49 |                      96609.139
 Lexus         | LX                      | 96096.30 |        97096.30 |                     105705.930
 Mazda         | Tribute                 | 17543.60 |        18543.60 |                      19297.960
 GMC           | Yukon XL 1500           | 73205.05 |        74205.05 |                      80525.555
 Mercedes-Benz | SL-Class                | 85151.99 |        86151.99 |                      93667.189
(more)
```

# ROUND
The ROUND() function rounds a number to a specified number of decimal places.
``` sql
test=# SELECT
    make, model, price,
    ROUND(price, 0) AS rounded_price,
    ROUND(price / 1000.0, 2) AS price_in_thousands
FROM car;
     make      |          model          |  price   | rounded_price | price_in_thousands 
---------------+-------------------------+----------+---------------+--------------------
 Alfa Romeo    | Spider                  | 82165.49 |         82165 |              82.17
 Toyota        | Tacoma                  | 57462.80 |         57463 |              57.46
 Lexus         | LX                      | 80665.18 |         80665 |              80.67
 Audi          | A6                      | 67269.82 |         67270 |              67.27
 Chrysler      | Concorde                | 84949.49 |         84949 |              84.95
 Buick         | Skylark                 | 12019.46 |         12019 |              12.02
 Mitsubishi    | Diamante                | 42435.56 |         42436 |              42.44
 Pontiac       | Firebird Formula        | 14905.84 |         14906 |              14.91
 BMW           | 7 Series                | 15042.85 |         15043 |              15.04
(more)
```

# Alias
SQL aliases are used to give a table, or a column in a table, a temporary name. Aliases are often used to make column names more readable. An alias only exists for the duration of that query. An alias is created with the AS keyword.
``` sql
test=# SELECT 
    make AS brand,
    model AS car_model,
    price AS cost
FROM car;
     brand     |        car_model        |   cost   
---------------+-------------------------+----------
 Alfa Romeo    | Spider                  | 82165.49
 Toyota        | Tacoma                  | 57462.80
 Lexus         | LX                      | 80665.18
 Audi          | A6                      | 67269.82
 Chrysler      | Concorde                | 84949.49
 Buick         | Skylark                 | 12019.46
 Mitsubishi    | Diamante                | 42435.56
 Pontiac       | Firebird Formula        | 14905.84
 BMW           | 7 Series                | 15042.85
 Aston Martin  | V8 Vantage S            | 34653.66
 Dodge         | Ram Van B250            | 87826.49
(more)
```


# COALESCE
The COALESCE() function returns the first non-null value in a list.
``` sql
test=# SELECT 
    COALESCE(email, 'No email')
FROM person;
```
output
```
             coalesce              
-----------------------------------
 adunsmuir0@rediff.com
 cend1@sciencedirect.com
 bgirke2@paypal.com
 dlapsley3@hhs.gov
 No email
 tbecraft5@i2i.jp
 tsouthall6@sphinn.com
 chawtrey7@upenn.edu
 kallport8@bandcamp.com
 ipittet9@deliciousdays.com
 No email
 cguntripb@tmall.com
(more)
```

# NULLIF
he NULLIF() function returns NULL if two expressions are equal, otherwise it returns the first expression.
```sql
SELECT 
    price,
    NULLIF(price, 0) AS non_zero_price
FROM car;
```

# Timestamps and Dates
```
test=# SELECT NOW()::DATE;
    now     
------------
 2025-06-16
(1 row)
```

# Adding and Subtracting with Dates
```
test=# SELECT NOW() - INTERVAL '1 YEAR';
           ?column?            
-------------------------------
 2024-06-16 23:48:24.264817+06
(1 row)

test=# SELECT NOW() + INTERVAL '1 MONTHS';
           ?column?            
-------------------------------
 2025-07-16 23:48:28.737361+06
(1 row)
```

# Age function
The PostgreSQL AGE() function subtracts two timestamps, producing a symbolic result that uses years and months.
``` sql
test=# SELECT *, AGE(NOW(), date_of_birth) AS Age FROM person;
  id  |  first_name  |      last_name      |               email               |   gender    | date_of_birth |          country_of_birth          |                   age                    
------+--------------+---------------------+-----------------------------------+-------------+---------------+------------------------------------+------------------------------------------
    1 | Amalie       | Dunsmuir            | adunsmuir0@rediff.com             | Female      | 2022-07-12    | Brazil                             | 2 years 11 mons 4 days 23:49:21.953712
    2 | Cyndia       | End                 | cend1@sciencedirect.com           | Female      | 2011-11-24    | Swaziland                          | 13 years 6 mons 22 days 23:49:21.953712
    3 | Bonnie       | Girke               | bgirke2@paypal.com                | Polygender  | 1983-02-02    | Philippines                        | 42 years 4 mons 14 days 23:49:21.953712
    4 | Danya        | Lapsley             | dlapsley3@hhs.gov                 | Female      | 2022-03-25    | China                              | 3 years 2 mons 22 days 23:49:21.953712
    5 | Maryl        | Prozillo            |                                   | Female      | 2015-09-12    | Armenia                            | 9 years 9 mons 4 days 23:49:21.953712
    6 | Tyson        | Becraft             | tbecraft5@i2i.jp                  | Male        | 1999-07-29    | Russia                             | 25 years 10 mons 18 days 23:49:21.953712
    7 | Tomi         | Southall            | tsouthall6@sphinn.com             | Agender     | 2013-05-04    | Chad                               | 12 years 1 mon 12 days 23:49:21.953712
    8 | Cris         | Hawtrey             | chawtrey7@upenn.edu               | Male        | 2019-08-20    | Indonesia                          | 5 years 9 mons 27 days 23:49:21.953712
    9 | Kial         | Allport             | kallport8@bandcamp.com            | Female      | 1988-11-03    | Indonesia                          | 36 years 7 mons 13 days 23:49:21.953712
   10 | Iris         | Pittet              | ipittet9@deliciousdays.com        | Genderqueer | 2023-03-18    | El Salvador                        | 2 years 2 mons 29 days 23:49:21.953712
   11 | Natalina     | Alcido              |                                   | Female      | 1996-09-10    | Chile                              | 28 years 9 mons 6 days 23:49:21.953712
(more)
```

# Check Constraints
The CHECK constraint is used to limit the value range that can be placed in a column.
If you define a CHECK constraint on a column it will allow only certain values for this column.
If you define a CHECK constraint on a table it can limit the values in certain columns based on values in other columns in the row.
``` sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    price NUMERIC CHECK (price > 0)
);
```

# Delete Records
The DELETE statement is used to delete existing records in a table.
``` sql
DELETE FROM person WHERE id = 10;
```

# Update Records
The UPDATE statement is used to update existing records in a table.
``` sql
UPDATE person SET email = 'maryl@hgv.com' WHERE id = 5;
```

# ON CONFLICT
PostgreSQL lets you either add or modify a record within a table depending on whether the record already exists. This is commonly known as an "upsert" operation (a portmanteau of "insert" and "update").

The actual implementation within PostgreSQL uses the INSERT command with a special ON CONFLICT clause to specify what to do if the record already exists within the table. You can specify whether you want the record to be updated if it's found in the table already or silently skipped.
* DO NOTHING: Tells PostgreSQL to leave the conflicting record as-is. In essence, this action makes no changes, but suppresses the error that would normally occur if you tried to insert a row that violates a condition.
* DO UPDATE: This tells PostgreSQL that you want to update the row that is already in the table. The syntax for the update mirrors that of the normal UPDATE command.
``` sql
test=# SELECT * FROM person WHERE id = 3;
 id | first_name | last_name |         email         |   gender   | date_of_birth | country_of_birth 
----+------------+-----------+-----------------------+------------+---------------+------------------
  3 | Bonnie     | Girke     | bgirke2@paypal.com.in | Polygender | 1983-02-02    | Philippines
(1 row)

test=# INSERT INTO person (id, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES ('3', 'John', 'Doe', 'bgirke2@google.com', 'Male', '2015-11-24', 'India') 
ON CONFLICT DO NOTHING;
INSERT 0 0
test=# INSERT INTO person (id, first_name, last_name, email, gender, date_of_birth, country_of_birth) 
VALUES ('3', 'John', 'Doe', 'bgirke2@google.com', 'Male', '2015-11-24', 'India') 
ON CONFLICT  (id) DO UPDATE SET email = EXCLUDED.email;
INSERT 0 1
test=# SELECT * FROM person WHERE id = 3;
 id | first_name | last_name |       email        |   gender   | date_of_birth | country_of_birth 
----+------------+-----------+--------------------+------------+---------------+------------------
  3 | Bonnie     | Girke     | bgirke2@google.com | Polygender | 1983-02-02    | Philippines
(1 row)
```

# CREATE FUNCTION
CREATE FUNCTION defines a new function. CREATE OR REPLACE FUNCTION will either create a new function, or replace an existing definition. To be able to define a function, the user must have the USAGE privilege on the language.

If a schema name is included, then the function is created in the specified schema. Otherwise it is created in the current schema. The name of the new function must not match any existing function or procedure with the same input argument types in the same schema. However, functions and procedures of different argument types can share a name (this is called overloading).

``` sql
CREATE FUNCTION get_customers_by_country(p_country_of_birth varchar)
 RETURNS TABLE(first_name varchar, last_name varchar)
 LANGUAGE SQL
 AS $$
     SELECT first_name, last_name
     FROM person
     WHERE country_of_birth = p_country_of_birth;
 $$;
```
output:
``` sql
SELECT * FROM get_customers_by_country('Russia');
+------------+-------------+
| first_name | last_name   |
|------------+-------------|
| Tyson      | Becraft     |
| Ruggiero   | MacMarcuis  |
| Bird       | Leynham     |
| Gnni       | Bound       |
| Bessie     | Josephi     |
| Celle      | McNeice     |
| Hillary    | Woodrup     |
| Brigitta   | Stoving     |
| Jocko      | Elvin       |
| Donni      | Bourgourd   |
| Catherine  | Gorse       |
| Val        | Kirkam      |
| Lenna      | Zambonini   |
| Kerby      | Warlaw      |
| Vassily    | Gillie      |
| Jilli      | McCrillis   |
| Kelcey     | Iacopini    |
(more)
```

# CREATE TRIGGER
CREATE TRIGGER creates a new trigger. CREATE OR REPLACE TRIGGER will either create a new trigger, or replace an existing trigger. The trigger will be associated with the specified table, view, or foreign table and will execute the specified function function_name when certain operations are performed on that table.

To replace the current definition of an existing trigger, use CREATE OR REPLACE TRIGGER, specifying the existing trigger's name and parent table. All other properties are replaced.

Here's a simple PostgreSQL TRIGGER example for your person table — let’s say you want to log whenever a new person is inserted into the table.

Create a logging table
``` sql
CREATE TABLE person_log (
     log_id serial PRIMARY KEY,
     person_id int,
     action varchar(20),
     log_time timestamp DEFAULT current_timestamp
 );
```

Create a trigger function
``` sql
CREATE FUNCTION log_person_insert()
 RETURNS trigger
 LANGUAGE plpgsql
 AS $$
 BEGIN
     INSERT INTO person_log (person_id, action)
     VALUES (NEW.id, 'INSERT');
     RETURN NEW;
 END;
 $$;
```
Create the trigger on the person table
``` sql
CREATE TRIGGER trg_log_person_insert
 AFTER INSERT ON person
 FOR EACH ROW
 EXECUTE FUNCTION log_person_insert();
CREATE TRIGGER
```
``` sql 
INSERT INTO person (
     first_name, 
     last_name, 
     email, 
     gender, 
     date_of_birth, 
     country_of_birth
 ) VALUES (
     'Test', 
     'User', 
     'test@example.com', 
     'Other', 
     '1990-01-01', 
     'Testland'
 );
```
``` sql
SELECT * FROM person_log;
+--------+-----------+--------+----------------------------+
| log_id | person_id | action | log_time                   |
|--------+-----------+--------+----------------------------|
| 1      | 1001      | INSERT | 2025-06-18 11:37:45.969819 |
+--------+-----------+--------+----------------------------+
SELECT 1
```

# Transactions
Transactions are a fundamental concept of all database systems. The essential point of a transaction is that it bundles multiple steps into a single, all-or-nothing operation. The intermediate states between the steps are not visible to other concurrent transactions, and if some failure occurs that prevents the transaction from completing, then none of the steps affect the database at all.

``` sql
test> BEGIN;
 INSERT INTO person (
     first_name, last_name, email, gender, date_of_birth, country_of_birth
 ) VALUES (
     'Bob', 'Jones', 'bob.jones@example.com', 'Male', '1988-02-20', 'USA'
 );
 INSERT INTO does_not_exists VALUES (1);
 COMMIT;
BEGIN
INSERT 0 1
relation "does_not_exists" does not exist
LINE 1: INSERT INTO does_not_exists VALUES (1)
                    ^
test> SELECT * FROM person where first_name='Bob'
current transaction is aborted, commands ignored until end of transaction block
```
Now You cannot run any more commands inside this failed transaction except ROLLBACK.

``` sql
test> ROLLBACK;
ROLLBACK
test> SELECT * FROM person where first_name='Bob'
+----+------------+-----------+-------+--------+---------------+------------------+
| id | first_name | last_name | email | gender | date_of_birth | country_of_birth |
|----+------------+-----------+-------+--------+---------------+------------------|
+----+------------+-----------+-------+--------+---------------+------------------+
SELECT 0
```

# Indexes
Indexes are a common way to enhance database performance. An index allows the database server to find and retrieve specific rows much faster than it could do without an index. But indexes also add overhead to the database system as a whole, so they should be used sensibly.

## Single-column Index
``` sql
CREATE INDEX idx_person_email ON person(email);
```
## Multi-column Index
``` sql
CREATE INDEX idx_person_fullname ON person(first_name, last_name);
```

Check Existing Indexes:
``` sql
SELECT indexname, indexdef 
 FROM pg_indexes 
 WHERE tablename = 'person';
+------------------+--------------------------------------------------------------------+
| indexname        | indexdef                                                           |
|------------------+--------------------------------------------------------------------|
| person_pkey      | CREATE UNIQUE INDEX person_pkey ON public.person USING btree (id)  |
| idx_person_email | CREATE INDEX idx_person_email ON public.person USING btree (email) |
+------------------+--------------------------------------------------------------------+
SELECT 2

```