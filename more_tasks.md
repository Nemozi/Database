
# DatabaseManagment - Translated Tasks

## 1. MySql tasks

### 1a.) Stored Function: `get_order_count_by_status`

You are tasked with writing a stored function as requested by management. The management needs a function `get_order_count_by_status` that queries the order status in the `Orders` table and returns the number of orders with this status. For example, when the function `get_order_count_by_status` is called with the status 'shipped', it should return the number of orders with that status. The count should be returned as `total_order`.

```sql
SELECT * FROM classic_cars.orders;

-- Example:
-- SELECT count(status) FROM classic_cars.orders
-- WHERE status = 'Cancelled';

CREATE OR REPLACE FUNCTION classic_cars.get_order_count_by_status(v_status VARCHAR(255), OUT total_order INT)
RETURNS INT 
AS $$
BEGIN
    SELECT count(ordernumber) INTO total_order
    FROM classic_cars.orders
    WHERE classic_cars.orders.status = v_status;
END;
$$ LANGUAGE plpgsql;

-- Call the function:
SELECT * FROM classic_cars.get_order_count_by_status('Cancelled');
```

### 1b.) Stored Procedure: `get_updateted_quantityinstock`

You are tasked with writing a stored procedure as requested by management. The management needs a procedure `get_updateted_quantityinstock` that automatically adjusts the `Quantityinstock` as products leave or enter the warehouse. The procedure should define two parameters: the `Productcode` and the number of products to be added or removed. When the procedure is called with the `Productcode` and quantity adjustment, the value of `Quantityinstock` in the `Products` table should automatically adjust. Both additions and subtractions must be taken into account.

```sql
SELECT * FROM classic_cars.products;

-- Examples:
SELECT productcode, quantityinstock FROM classic_cars.products
WHERE productcode = 'S10_1678';

SELECT productcode, quantityinstock FROM classic_cars.products
WHERE productcode = 'S10_1949';

CREATE OR REPLACE PROCEDURE classic_cars.get_updateted_quantityinstock(v_productcode VARCHAR(255), v_quantity INT)
AS $$
BEGIN
    UPDATE classic_cars.products
    SET quantityinstock = quantityinstock + v_quantity
    WHERE productcode = v_productcode;
END;
$$ LANGUAGE plpgsql;

-- Call the procedure:
CALL classic_cars.get_updateted_quantityinstock('S10_1949', -5);
```

## MySql taks

### 2a.) Stored Function: `get_profit`

In the `Products` table, for each product, the expected sales price (`msrp`) and the purchase price (`buyprice`) are stored. You are tasked with writing a function `get_profit`, which returns the `MSRP`, `Buyprice`, and expected profit when the `Productcode` is passed as a parameter. The profit is calculated as:

```
profit = msrp - buyprice
```

```sql
SELECT * FROM classic_cars.products;

CREATE OR REPLACE FUNCTION classic_cars.get_profit(v_productcode VARCHAR, OUT v_profit DOUBLE PRECISION, OUT v_msrp DOUBLE PRECISION, OUT v_buyprice DOUBLE PRECISION)
RETURNS RECORD
AS $$
BEGIN
    SELECT msrp, buyprice
    INTO v_msrp, v_buyprice
    FROM classic_cars.products
    WHERE productcode = v_productcode;

    v_profit = v_msrp - v_buyprice;
END;
$$ LANGUAGE plpgsql;

-- Call the function:
SELECT * FROM classic_cars.get_profit('S10_2016');
```

### 2b.) Conditional Profit Information

Based on the value of the profit, the function should return different information:

1. If the profit is greater than or equal to 0:  
   `'Ihre Eingaben: msrp = … €, buyprice = … €. Der Gewinn beträgt: … €.'`

2. If the profit is less than 0:  
   `'Ihre Eingaben: msrp = … €, buyprice = … €. Der Verlust beträgt: … €.'`

3. In all other cases, an error message should be returned:  
   `'Überprüfen Sie bitte Ihre Eingaben.'`

```sql
SELECT * FROM classic_cars.products;

CREATE OR REPLACE FUNCTION classic_cars.get_profit(v_productcode VARCHAR, OUT v_profit DOUBLE PRECISION, OUT v_msrp DOUBLE PRECISION, OUT v_buyprice DOUBLE PRECISION)
RETURNS RECORD
AS $$
BEGIN
    SELECT msrp, buyprice
    INTO v_msrp, v_buyprice
    FROM classic_cars.products
    WHERE productcode = v_productcode;

    v_profit = v_msrp - v_buyprice;

    IF v_profit >= 0 THEN
        RAISE INFO 'Ihre Eingaben: msrp = % €, buyprice = % €. Der Gewinn beträgt: % €.', v_msrp, v_buyprice, v_profit;
    ELSIF v_profit < 0 THEN
        RAISE INFO 'Ihre Eingaben: msrp = % €, buyprice = % €. Der Verlust beträgt: % €.', v_msrp, v_buyprice, v_profit;
    ELSE
        RAISE EXCEPTION 'Überprüfen Sie bitte Ihre Eingaben.';
    END IF;
END;
$$ LANGUAGE plpgsql;

-- Call the function:
SELECT * FROM classic_cars.get_profit('S10_2016');
SELECT * FROM classic_cars.get_profit('S10_1949');
SELECT * FROM classic_cars.get_profit('');
```
