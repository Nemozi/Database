# Classic Cars Database Tasks and SQL Queries

## 1. Creating Tables and Importing Data from CSV Files

### Products Table
```sql
CREATE TABLE classic_cars.products (
    productCode VARCHAR(255) PRIMARY KEY,
    productName VARCHAR(255),
    productLine VARCHAR(255),
    productScale VARCHAR(255),
    productVendor VARCHAR(255),
    productDescription TEXT,
    quantityInStock INT,
    buyPrice FLOAT,
    MSRP FLOAT
);

SELECT * FROM classic_cars.products;
```

### Customers Table
```sql
CREATE TABLE classic_cars.customers (
    customerNumber INT PRIMARY KEY,
    customerName VARCHAR(255),
    contactLastName VARCHAR(255),
    contactFirstName VARCHAR(255),
    phone TEXT,
    addressLine1 VARCHAR(255),
    addressLine2 VARCHAR(255),
    city VARCHAR(255),
    state VARCHAR(255),
    postalCode VARCHAR(255),
    country VARCHAR(255),
    salesRepEmployeeNumber VARCHAR(255),
    creditLimit INT
);

SELECT * FROM classic_cars.customers;
```

### Order Details Table
```sql
CREATE TABLE classic_cars.orderdetails (
    ordernumber INT NOT NULL,
    productCode VARCHAR(255) NOT NULL,
    quantityOrdered INT,
    priceEach FLOAT,
    orderLineNumber INT
);

SELECT * FROM classic_cars.orderdetails;
```

### Employees Table
```sql
CREATE TABLE classic_cars.employees (
    employeeNumber INT PRIMARY KEY,
    lastName VARCHAR(255),
    firstName VARCHAR(255),
    extension VARCHAR(255),
    email VARCHAR(255),
    officeCode INT,
    reportsTo VARCHAR(255) NULL,
    jobTitle VARCHAR(255)
);

SELECT * FROM classic_cars.employees;
```

### Orders Table
```sql
CREATE TABLE classic_cars.orders (
    ordernumber INT PRIMARY KEY,
    orderDate DATE,
    requiredDate DATE,
    shippedDate VARCHAR(255),
    status VARCHAR(255),
    comments TEXT,
    costumerNumber INT
);

SELECT * FROM classic_cars.orders;
```

### Offices Table
```sql
CREATE TABLE classic_cars.offices (
    officeCode INT PRIMARY KEY,
    city VARCHAR(255),
    phone VARCHAR(255),
    adressLine1 VARCHAR(255),
    adressLine2 VARCHAR(255),
    state VARCHAR(255),
    country VARCHAR(255),
    postalcode VARCHAR(255),
    territory VARCHAR(255)
);

SELECT * FROM classic_cars.offices;
```

## 2. Management Reporting

### a) Customer Distribution by Country and Average Credit Limit
Task: The management wants to know how many customers come from which countries. They also want to see the average credit limit per country. The result should be sorted by descending count, then by descending average credit limit.

```sql
SELECT country, COUNT(customerName), AVG(creditLimit)
FROM classic_cars.customers
GROUP BY country
ORDER BY COUNT(customerName) DESC, AVG(creditLimit) DESC;
```

### b) September 2003 Orders
Task: The management wants to know which customer numbers placed an order in September 2003. They also want to determine the number of orders for each customer in this month.

```sql
SELECT customerNumber, COUNT(customerNumber)
FROM classic_cars.orders
WHERE orderDate BETWEEN '2003-09-01' AND '2003-09-30'
GROUP BY customerNumber;
```

### c) Vehicle Inventory Overview
Task: The management wants an overview of the vehicles in stock. Write an SQL command to determine the total quantity of all stocks (= quantityInStock) per product line and sort the result by descending sum. The output should show the product line and the sum of vehicles.

```sql
SELECT productLine, SUM(quantityInStock)
FROM classic_cars.products
GROUP BY productLine
ORDER BY SUM(quantityInStock) DESC;
```

### d) Pamela Castillo's Office Location
Task: The management wants to organize a party for employee Pamela Castillo's 15th company anniversary. They don't know which office she's assigned to. Help the management by finding the location of her assigned office, including street, city, and country.

```sql
SELECT lastName, firstName, adressLine1, country, postalCode
FROM classic_cars.employees 
INNER JOIN classic_cars.offices ON employees.officeCode = offices.officeCode
WHERE lastName = 'Castillo';
```

### e) Customer 385 Information
Task: The customer with customer number 385 has seemed suspicious to the management. They want to know the first name and last name of the customer's contact. They also want you to provide the order numbers that can be assigned to this customer.

```sql
SELECT customers.customerNumber, contactLastName, contactFirstName, orderNumber 
FROM classic_cars.customers
INNER JOIN classic_cars.orders ON customers.customerNumber = orders.customerNumber
WHERE customers.customerNumber = 385;
```

### f) Product Vendor Analysis
Task: The management wants to know the names of all sellers (= productVendor) of their products. They also want to know the number of products in stock from each seller, as well as the maximum purchase price per seller output as the maximum price at which the product is purchased. The result should be sorted by descending maximum purchase price.

```sql
SELECT productVendor, COUNT(*) AS productCount, MAX(buyPrice) AS maxBuyPrice, SUM(quantityInStock) AS totalStock
FROM classic_cars.products
GROUP BY productVendor
ORDER BY maxBuyPrice DESC;
```

### g) Unique Customer Cities
Task: From which cities do our company's customers come? Each city should be displayed only once!

```sql
SELECT DISTINCT city FROM classic_cars.customers;
```

### h) Special Offer Eligibility
Task: The management wants to make a special offer to certain customers. The only condition for receiving this offer is that the customer's credit limit must be greater than the average credit limit of all customers. The management needs the customer name, address line 1, the country the company comes from, and the customer's credit limit. The result should be sorted by descending credit limit.

```sql
SELECT customerName, addressLine1, country, creditLimit
FROM classic_cars.customers
WHERE creditLimit > (SELECT AVG(creditLimit) FROM classic_cars.customers)
ORDER BY creditLimit DESC;
```

### i) Product Profit Calculation
Task: The management calculates the profit of a product from the difference between MSRP and buyPrice from the product table. They need the profit per product in an output within the variable 'Profit'. For this profit, the information productCode, productName, buyPrice, and MSRP from the product table are needed. The profit should be sorted in descending order.

```sql
SELECT productCode, productName, buyPrice, MSRP, (MSRP - buyPrice) AS Profit
FROM classic_cars.products
ORDER BY Profit DESC;
```
