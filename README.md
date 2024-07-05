ABC Shop Database Project
Introduction

This project involves creating a relational database for ABC Shop using MySQL. The database stores information about customers, suppliers, products, orders, and returns. This README file provides step-by-step instructions to set up and run the database on your local system.
Prerequisites

Before you begin, ensure you have the following software installed on your system:

    XAMPP - An easy-to-install Apache distribution containing MySQL, PHP, and Perl. Download and install it from here.

    MySQL Workbench - A unified visual tool for database architects, developers, and DBAs. Download and install it from here.

Setup Instructions
Step 1: Start XAMPP

    Open XAMPP Control Panel.
    Start the Apache and MySQL modules.

Step 2: Create the Database

    Open MySQL Workbench and connect to your local MySQL server.

    Create a new schema (database) by executing the following SQL command:

    sql

    CREATE DATABASE abc_shop;
    USE abc_shop;

Step 3: Create Tables

Run the following SQL commands in MySQL Workbench to create the necessary tables:

sql

-- Create Customer Table
CREATE TABLE Customer (
    Customer_id INT AUTO_INCREMENT PRIMARY KEY,
    Fullname VARCHAR(100),
    Emailaddress VARCHAR(100) UNIQUE,
    Lastlogintime DATETIME,
    Password VARCHAR(255),
    Address VARCHAR(255),
    Telephonenumber VARCHAR(15)
);

-- Create Supplier Table
CREATE TABLE Supplier (
    Supplier_id INT AUTO_INCREMENT PRIMARY KEY,
    Contact_info VARCHAR(100),
    Address VARCHAR(255),
    Supplier_name VARCHAR(100)
);

-- Create Product Table
CREATE TABLE Product (
    Product_id INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(100),
    Price DECIMAL(10,2),
    Product_description TEXT,
    Product_category VARCHAR(50),
    Supplier_id INT,
    Images TEXT,
    Tags VARCHAR(255),
    Comments TEXT,
    FOREIGN KEY (Supplier_id) REFERENCES Supplier(Supplier_id)
);

-- Create Orders Table
CREATE TABLE Orders (
    Order_id INT AUTO_INCREMENT PRIMARY KEY,
    Customer_id INT,
    Order_date DATETIME,
    Total_amount DECIMAL(10, 2),
    Order_description VARCHAR(255),
    FOREIGN KEY (Customer_id) REFERENCES Customer(Customer_id)
);

-- Create OrderItems Table
CREATE TABLE OrderItems (
    Order_items_id INT AUTO_INCREMENT PRIMARY KEY,
    Order_id INT,
    Product_id INT,
    Quantity INT,
    Price DECIMAL(10, 2),
    FOREIGN KEY (Order_id) REFERENCES Orders(Order_id),
    FOREIGN KEY (Product_id) REFERENCES Product(Product_id)
);

-- Create Return Table
CREATE TABLE `Return` (
    Return_id INT AUTO_INCREMENT PRIMARY KEY,
    Orderitem_id INT,
    Returndate DATETIME,
    Reason TEXT,
    Product_id INT,
    Refundamount DECIMAL(10, 2),
    FOREIGN KEY (Orderitem_id) REFERENCES OrderItems(Order_items_id)
);

Step 4: Insert Sample Data

Run the following SQL commands to insert sample data into the tables:

sql

-- Insert Customers
INSERT INTO Customer (FullName, EmailAddress, LastLoginTime, Password, Address, TelephoneNumber)
VALUES 
('Krishan', 'krishan123@gmail.com', '2024-06-30 12:00:00', 'Krishan23', 'New Delhi, India', '+91911111111'),
('Leonardo Perkins', 'l.perkins@gmail.com', '2024-06-30 10:00:00', 'password123', 'Unknown Address', '988-6015-53'),
-- ... Add other customers as needed ...

-- Insert Suppliers
INSERT INTO Supplier (Supplier_name, Contact_info, Address)
VALUES 
('Mishra Electronics', 'mishra.electronics@gmail.com', '101 Tech Park, Bengaluru, Karnataka, India'),
('Gupta Fashion House', 'gupta.fashion@gmail.com', '202 Fashion Street, Mumbai, Maharashtra, India'),
-- ... Add other suppliers as needed ...

-- Insert Products
INSERT INTO Product (Name, Supplier_id, Product_description, Price, Product_category, Images, Tags, Comments)
VALUES 
('Lays Classic', 1, 'Classic salted potato chips', 1.99, 'Snacks', 'lays_classic.png', 'chips, snacks', 'Crispy and salty, a timeless classic.'),
('Lays Sour Cream & Onion', 2, 'Potato chips with sour cream and onion flavor', 2.49, 'Snacks', 'lays_sour_cream_onion.png', 'Tangy and creamy flavor.'),
-- ... Add other products as needed ...

-- Insert Orders
INSERT INTO Orders (Customer_id, Order_date, Total_amount, Order_description)
VALUES 
(1, '2023-06-15 10:00:00', 10.47, 'Order of Lays Classic and Barbecue chips'),
(2, '2023-06-16 11:00:00', 4.98, 'Order of Lays Classic chips'),
-- ... Add other orders as needed ...

-- Insert OrderItems
INSERT INTO OrderItems (Order_id, Product_id, Quantity, Price)
VALUES 
(1, 1, 2, 1.99),
(1, 2, 1, 2.49),
-- ... Add other order items as needed ...

-- Insert Returns
INSERT INTO `Return` (Orderitem_id, Product_id, Returndate, Refundamount, Reason)
VALUES 
(1, 1, '2023-06-20', 1.99, 'Defective product'),
(2, 2, '2023-06-21', 2.49, 'Not as expected'),
-- ... Add other returns as needed ...

Step 5: Execute Queries

You can execute the following queries to retrieve various reports:

    Detail information about suppliers and the number of products they provide:

    sql

SELECT 
    s.Supplier_id,
    s.Supplier_name,
    COUNT(p.Product_id) AS Number_of_Products
FROM 
    Supplier s
LEFT JOIN 
    Product p ON s.Supplier_id = p.Supplier_id
GROUP BY 
    s.Supplier_id, s.Supplier_name;

10 best-selling products with the total amount and their supplier:

sql

SELECT 
    p.Product_id,
    p.Name AS Product_Name,
    s.Supplier_name,
    SUM(oi.Quantity) AS Total_Quantity_Sold,
    SUM(oi.Quantity * oi.Price) AS Total_Amount
FROM 
    OrderItems oi
JOIN 
    Product p ON oi.Product_id = p.Product_id
JOIN 
    Supplier s ON p.Supplier_id = s.Supplier_id
GROUP BY 
    p.Product_id, p.Name, s.Supplier_name
ORDER BY 
    Total_Quantity_Sold DESC
LIMIT 10;

List of customers and their total purchases:

sql

SELECT 
    c.Customer_id,
    c.Fullname,
    SUM(o.Total_amount) AS Total_Purchases
FROM 
    Customer c
JOIN 
    Orders o ON c.Customer_id = o.Customer_id
GROUP BY 
    c.Customer_id, c.Fullname;

List of returned items:

sql

SELECT 
    r.Return_id,
    oi.Order_id,
    p.Product_id,
    p.Name AS Product_Name,
    r.Returndate,
    r.Refundamount,
    r.Reason
FROM 
    `Return` r
JOIN 
    OrderItems oi ON r.Orderitem_id = oi.Order_items_id
JOIN 
    Product p ON r.Product_id = p.Product_id;

List of products in the fashion category that were sold last month:

sql

    SELECT 
        p.Product_id,
        p.Name AS Product_Name,
        oi.Quantity,
        oi.Price,
        oi.Order_id,
        o.Order_date
    FROM 
        Product p
    JOIN 
        OrderItems oi ON p.Product_id = oi.Product_id
    JOIN 
        Orders o ON oi.Order_id = o.Order_id
    WHERE 
        p.Product_category = 'Fashion'
        AND o.Order_date >= '2006-06-01'
        AND o.Order_date < '2006-08-01';

Conclusion

This README provides the necessary steps to set up and run the ABC Shop database project on your local system. By following these instructions, you will have a functional relational database system that supports various business operations and data analysis tasks.
