# **MySQL Cheat Sheet**

## Database

- Create database


## Table

- Create table
    ```
    CREATE TABLE <table_name>
    (
        id              int unsigned NOT NULL auto_increment, # Unique ID for the record
        title           varchar(255) NOT NULL,                # Title of the record

    PRIMARY KEY     (id)
    );
    ```
- Show tables
    ```
     show tables;
    ```

- Describe tables: DESCRIBE shows information on all columns of a table
    ```
    DESCRIBE <table_name>;
    ```

- Explain tables: EXPLAIN is used to obtain a query execution plan (that is, an explanation of how MySQL would execute a query). Synonym with DESCRIBE statement.
    ```
    EXPLAIN <table_name>;
    ```

- ALTER TABLE: Add or delete a column from a table.  
Use an ALTER TABLE...ADD statement to add a column. You can use, for example, an AFTER clause to specify the location of the new column
    ```
    ALTER TABLE <table_name> ADD <new_col> CHAR(1) AFTER <col_name>;
    ```

    ```
    ALTER TABLE <table_name> DROP <col_name>;
    ```

## Record

- Add records
    ```
    INSERT INTO <table_name> ( <col1>, <col2> ) VALUES ( "Hello", "World1" ), ( "Hello", "World2" );
  ```

- Delete records
    ```
    DELETE FROM <table_name> WHERE criteria;
    ```

- Retrieve records
   
    ```
    SELECT 
        column_1, column_2, ...
    FROM
        table_1                     required
    ----------------------------------------
                                    optional
    [INNER | LEFT |RIGHT] JOIN table_2 ON conditions
    WHERE
        conditions
    GROUP BY column_1
    HAVING group_conditions
    ORDER BY column_1 [ASC|DESC], column_2 [ASC|DESC], ...
    or) ORDER BY column_1 * columan_2
    LIMIT offset, length;
    ```
    
    
    `SELECT`
    - followed by a list of comma-separated columns or an asterisk (*) to indicate that you want to return all columns.
    
    `SELECT DISTINCT`
    - In order to remove these duplicate rows, you use the DISTINCT clause in the SELECT statement.

    `FROM`
    - specifies the table or view where you want to query the data.

    `JOIN`
    - gets related data from other tables based on specific join conditions.

    `WHERE`
    - clause filters row in the result set.
    - `IN` Operator: The `IN` operator allows you to determine if a specified value matches any value in a set of values or returned by a subquery.
        ```
        WHERE (expr|column_1) IN ('value1','value2',...);
        ```

        - The `IN` operator is often used with a subquery. 
            ```
            ... WHERE col_1 IN (SELECT col_2 FROM... )
            ```
    - `LIKE` Operator: The LIKE operator allows you to select data from a table based on a specified pattern.
        - The percentage ( `%` ) wildcard: allows you to match any string of zero or more characters.
        - The underscore ( `_` ) wildcard: allows you to match any single character.
            ```
            ex) ... WHERE firstName LIKE 'a%';
            => find whose first name starts with character a
            ```
        - Escape pattern : If need to find '%' and '_' itself, escape by `\` or `$` with `ESCAPE`.
            ```
            WHERE productCode LIKE '%\_20%';
            WHERE productCode LIKE '%$_20%' ESCAPE '$';
            => The pattern %$_20% matches any string that contains the _20 string.
            ```
    - `EXISTS`: 
        - The EXISTS operator is a Boolean operator that returns either true or false. The EXISTS operator is often used the in a subquery to test for an “exist” condition.
        - The EXISTS operator *terminates* further processing immediately once it finds a matching row. Because of this characteristic, you can use the EXISTS operator to improve the *performance* of the query in some cases
            ```
            ex) Find the customer who has placed at least one sales order:
            SELECT customerNumber, customerName
            FROM customers
            WHERE
                EXISTS( SELECT 1
                        FROM orders
                        WHERE orders.customernumber = customers.customernumber);
            ```
        - `EXISTS` vs `IN` : The general rule of thumb is that if the subquery contains a large volume of data, the *EXISTS operator provides better performance*. The reason is that the EXISTS operator works based on the *“at least found”* principle. It returns true and stops scanning table once at least one matching row found.
    


    `GROUP BY`: Groups a set of rows into groups and applies aggregate functions on each group.
    
    `HAVING`: Filters group based on groups defined by GROUP BY clause.

    `ORDER BY`: Specifies a list of columns for sorting.

    `LIMIT`: Constrains the number of returned rows.
    -  The `offset` specifies the offset of the first row to return. The offset of the first row is 0, not 1.
    - The `count` specifies the maximum number of rows to return.
        ```
        SELECT column1,column2,...
        FROM table
        LIMIT count;
        or
        LIMIT offset , count; 
        ```

        ```
        ex) Get the nth highest value
        SELECT column1, column2,...
        FROM table
        ORDER BY column1 DESC
        LIMIT nth-1, 1;
        ```
            

- Column & Table alias
    - `SELECT col_1 * col_2 AS new_col` : `AS` will be working as *column alias*
    -   Use ` to make alias or declare right after 
        ```
        SELECT orderNumber `Order no.`,
               SUM(priceEach * quantityOrdered) total
        FROM customers c
        ```
      
## Joining tables
- INNER JOIN: Matches rows in one table with rows in other tables and allows you to query rows that contain columns from both tables
    ```
    SELECT column_list
    FROM t1
    INNER JOIN t2 ON join_condition1
    INNER JOIN t3 ON join_condition2
    ...
    WHERE where_conditions;
    ```



    

## MySQL functions
- CAST function: The CAST() function converts a value of any type into a value that has a specified type. The target type can be any one of the following types: BINARY, CHAR, DATE, DATETIME, TIME,DECIMAL, SIGNED, UNSIGNED 
    ```
    CAST(expression AS TYPE);
    ex) ... WHERE requiredDate BETWEEN CAST('2003-01-01' AS DATETIME)
                        AND CAST('2003-01-31' AS DATETIME);
    ```


- Aggregate functions
    - COUNT function: The COUNT function returns the number of the rows in a table. For example, you can use the COUNT function to get the number of products in the products table as the following query:
        ```
        SELECT COUNT(*) AS Total FROM products
        ```

- Concatenate function
    - CONCAT_WS function: Concatenate words. 
        - ex) CONCAT_WS(', ', lastName, firstname) = lastname + , + firstname



## Execute sql statements from a text file
- From mysql (`The MySQL command-line client which is a simple SQL shell`)
    ```
    source /Users/will/Study/MySQL/books.sql
    ```






## Reference
- https://dev.mysql.com/doc/mysql-getting-started/en/
- https://www.elated.com/mysql-for-absolute-beginners/
- http://www.mysqltutorial.org
