---
tags:
  - PL-SQL
  - DBMS
  - OracleSQL
  - SQL
  - Triggers
Author: Prem Dharshan D
Date: 2023-10-01T03:30:00
---
---
## Problem statements

### Problem Statement 1:

#### Problem Statement: Employee Management with Triggers 

You are tasked with managing an employee database in India. Create a PL/SQL trigger that ensures the following:

- When a new employee is inserted into the "employees" table, their salary should not exceed ₹10,000.
- If an attempt is made to insert an employee with a salary above ₹10,000, the trigger should automatically set their salary to ₹10,000.

Write a PL/SQL trigger to accomplish this task.

#### Input Format:

- An INSERT statement that adds a new employee to the "employees" table.

```
INSERT INTO employees (employee_id, first_name, last_name, salary) VALUES (102, 'Raj', 'Patel', 12500);
```

#### Constraints:

New employees must have a salary below or equal to ₹10,000.
#### DDL: 

```
CREATE TABLE employees
(   employee_id NUMBER(5) PRIMARY KEY,
	first_name VARCHAR2(50),
	last_name VARCHAR2(50),
	salary NUMBER(7, 2)
);
```



#### Output Format:

-  The trigger should automatically modify the inserted salary if it exceeds ₹10,000.

| employee_id | first_name | last_name | salary |
|:------------|:-----------|:----------|:-------|
| 102         | Raj        | Patel     | 10000  |

#### Solution

```
CREATE OR REPLACE TRIGGER Salary_Constraint
BEFORE INSERT ON employees
FOR EACH ROW 
BEGIN 
	IF :new.salary > 10000 THEN
		:new.salary := 10000; 
	END IF;
END; 
/
```

### Problem Statement 2:

#### Problem Statement: Inventory Stock Management with Triggers

You are working for a retail company with a large inventory of products. The company's database includes an "inventory" table that tracks product stock levels. Your task is to implement a robust stock management system using PL/SQL triggers.
###### Requirements:

1. When a new product is added to the "inventory" table, ensure that the product's stock level is within an acceptable range.
2. The acceptable stock range for products is between 10 units and 100 units (inclusive).
3. If an INSERT statement attempts to add a product with stock levels outside this range, the trigger should raise an exception and prevent the insertion.
4. Additionally, if the stock level of an existing product is updated to fall outside the acceptable range, the trigger should also raise an exception and prevent the update.
5. The trigger should log any such exception-raising events in an "inventory_exceptions" table, including details about the product and the reason for the exception.

Write a PL/SQL trigger to enforce the above requirements for stock management. Ensure that the trigger prevents invalid data from being inserted or updated in the "inventory" table and logs exceptions in the "inventory_exceptions" table.
#### DDL:

**1.  Inventory:**  This table contains product information, including product_id, product_name, stock_level, and other details.

```
CREATE TABLE inventory 
(
  product_id NUMBER(5) PRIMARY KEY,
  product_name VARCHAR2(100),
  stock_level NUMBER(5)
);
```

**2. Inventory_exceptions:** This table is used to log exceptions raised by the trigger.

```
CREATE TABLE inventory_exceptions 
(
  exception_id NUMBER(5) PRIMARY KEY,
  product_id NUMBER(5),
  exception_reason VARCHAR2(200)
);
```

#### Input Format:

- For new product insertions, you will receive an INSERT statement with the product details.
- For stock level updates, you will receive an UPDATE statement.
#### Output Format:

- The trigger should prevent invalid data insertion or updates.
- Exceptions should be logged in the "inventory_exceptions" table with appropriate details.

#### Constraints:

-  The acceptable stock range is between 10 units and 100 units (inclusive).
#### Test Cases:

##### **Test Case 1:** DML Statement (Insertion)

```
-- Attempt to insert a new product with an invalid stock level (5 units).
INSERT INTO inventory (product_id, product_name, stock_level)
VALUES (101, 'Product A', 5);
```

**Expected Output:**
The trigger should raise an exception. An entry should be logged in the "inventory_exceptions" table with the exception reason.

##### Test Case 2: DML Statement (Update):

```
-- Attempt to update an existing product's stock level to an invalid value (150 units).
UPDATE inventory
SET stock_level = 150
WHERE product_id = 101;
```

**Expected Output:**
The trigger should raise an exception.
An entry should be logged in the "inventory_exceptions" table with the exception reason.

#### Solution:

```
-- Create the trigger
CREATE OR REPLACE TRIGGER Stock_Level_Constraint
BEFORE INSERT OR UPDATE ON inventory
FOR EACH ROW
BEGIN
  IF :new.stock_level < 10 OR :new.stock_level > 100 THEN
    -- Raise an exception and prevent invalid data insertion or updates
    RAISE_APPLICATION_ERROR(-20001, 'Stock level must be between 10 and 100 units.');
  END IF;
EXCEPTION
  WHEN OTHERS THEN
    -- Log the exception in the inventory_exceptions table
    INSERT INTO inventory_exceptions (product_id, exception_reason)
    VALUES (:new.product_id, SQLERRM);
END;
/
```

In both test cases, the trigger should prevent invalid data insertion or updates to the "inventory" table and log exceptions in the "inventory_exceptions" table as specified in the problem statement.

### Problem Statement 3:

#### Problem Statement: Enhancing Sales Orders with Triggers

You are tasked with managing a sales order database for a retail company. To improve order processing efficiency, create PL/SQL triggers to automate certain tasks: 

**1. Auto-Calculate Total Price:** Create a trigger that calculates the total price of each sales order automatically. The trigger should fire before an INSERT statement on the "sales_orders" table. The total price is calculated as the sum of the prices of all items in the order.

**2. Auto-Assign Discount:** Create a trigger that automatically assigns a discount to orders with a total price exceeding ₹5,000. The trigger should fire before an INSERT statement on the "sales_orders" table. The discount should be 10% of the total price.

**3. Update Customer Credit:** Create a trigger that updates the customer's credit limit in the "customers" table when a new sales order is added. If the total price of the order exceeds the customer's current credit limit, decrease the customer's credit limit by the total price of the order.

Write the PL/SQL triggers to accomplish these tasks.







## Resources and References

1. [[PL_SQL .pdf]] - Dr. Thilaga M, Associate Professor, PSGCT.
2. [[Oracle9i Application Developer’s Guide - Fundamentals.pdf#page=567]] - Copyright © 1995, 2023, Oracle and/or its affiliates.

---

