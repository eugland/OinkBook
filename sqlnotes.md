# sql Notes


To get the N'th element in a sql query

```sql
SELECT salary FROM salary
ORDERED BY DESC
LIMIT 1 
OFFSET n-1
```

## Constraint
If we want to define a Check constraint that will only allow stock item more than 0 and less than 50:
```sql
CREATE TABLE item 
(
    item VARCHAR(200),
    stock INTEGER,
    CONSTRAINT valid_stock check(stock > 0 and stock < 50)
);
/*This will succeed*/
INSERT INTO item (item, stock)
VALUES 
    ('broom', 1),
    ('stick', 49);

/*This will fail*/
INSERT INTO item (item, stock)
VALUES 
    ('broom', 1),
    ('stick', 49);
/*This will fail*/
```

The following is the same, notice the inline check statement:
```sql
CREATE TABLE item
(
	item VARCHAR(100) PRIMARY KEY,
    stock INTEGER check(stock < 50 AND stock > 0)
);
```

Use check to list acceptable values: 

```sql
CREATE TABLE item
(
	book VARCHAR(100) PRIMARY KEY,
    genre VARCHAR(100) check(genre in ('DRAMA', 'HORROR'))
);
```

The BETWEEN keyword

```sql 
CREATE TABLE small_number
(
    stock INTEGER check (stock BETWEEN 0 AND 100)
);
```


## Assertions
constraints that apply to multiple tables, 
so you cannot create assertions while definint a table, 
CREATE them afterwards
IT is like a table level check constraint


The following assertion ensures total book stock is less than 5000
OOPS CREATE ASSERTION IS NOT SUPPORTED BY MOST PROGRAMS

```sql 
CREATE TABLE book(
    name TEXT PRIMARY KEY,
    stock INTEGER check (stock BETWEEN 0 AND 4000),
    CREATE ASSERTION LIMIT_STOCK check ( 
        (SELECT SUM FROM book) < 5000
    )
);
```

Instead Configure a trigger
```sql
DELIMITER //

CREATE TRIGGER check_total_books
BEFORE INSERT ON book
FOR EACH ROW
BEGIN 
    IF ( SELECT SUM(stock) FROM book) > 5000 THEN 
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Total books exceed 5,000';
    END IF;
END;
//
DELIMITER ;
```


## Domain Constraint
```sql
CREATE DOMAIN stock AS INTEGER 
DEFAULT 0
check (VALUE BETWEEN 0 and 100);
```

## SQL VIEW
a sql view is bascially virtual tables that does not persist data but persist the query that uses to look up the data. 
A sql view does not exist physically as a table. 
But to use a view you can just use it like a table. 
You use a sql view to gate access to unwanted information.

```sql
CREATE VIEW EmployeeName (
    firstname, 
    lastname
) AS
SELECT 
    firstname, 
    lastname 
FROM Employees;
```

## Perform options on a sql statement
```SQL
CREATE VIEW book_discounts AS
SELECT 
    book_title, 
    price * 0.8 AS reduced_price
FROM book_inventory
```

## Cartesian Join when the On condition fucks up
Examine the code below
```sql
SELECT 
    b.bookid, b.title, b.price, d.reduced_price
FROM BookInventory AS b 
LEFT JOIN DiscountedPrice AS d
ON b.bookid = b.bookid;
```
because the condition b.bookid = b.bookid always evaluates to true for every row in b, every row from BookInventory is combined with every row from DiscountedPrice. This


## Test if a view is updatable
ERROR:  View columns that are not columns of their base relation are not updatable.cannot update column "reduced_price" of view "discountedprice" 

ERROR:  cannot update column "reduced_price" of view "discountedprice"
SQL state: 0A000
Detail: View columns that are not columns of their base relation are not updatable.



## USE SELECT statement to create a new table from an old table

```sql
CREATE TABLE board AS
SELECT id, position FROM stockholder WHERE position = 'board';
```


## COMPARATOR operator
```sql
SELECT AColumn_Name1, AColumn_Name2 A
FROM ATable_Name A
WHERE AColumn_Name3 ABETWEEN A100 AAND A1000 A
GREATER ATHAN, ALESS ATHAN, ALESS ATHAN AOR AEQUAL ATO, AGREATER
ATHAN AOR AEQUAL ATO A
```

Using Likes to find string at the beginning, middle, or end in a column
1. Find products with phone anywhere in their name
2. Find products whose names start with "Note" 