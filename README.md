# Pizza Delivery API with XMysql

This project demonstrates the use of XMysql to automatically generate a RESTful API from a MySQL database. The implementation creates a complete pizza delivery system API with tables for pizzas, customers, orders, and order items.

## Tool Used
[XMysql](https://github.com/o1lab/xmysql) 
## Implementation Steps

1. **Set up Digital Ocean CentOS droplet**
   - Created a CentOS server on Digital Ocean
   - Connected via SSH

2. **Installed Required Dependencies**
   ```bash
   sudo yum update -y
   sudo yum install -y nodejs npm
   sudo yum install -y mysql-server
   sudo systemctl start mysqld
   sudo systemctl enable mysqld
   sudo mysql_secure_installation

3. SQL Database
CREATE DATABASE pizza_delivery;
USE pizza_delivery;

CREATE TABLE customers (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  phone VARCHAR(20),
  address VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE pizzas (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  description TEXT,
  price DECIMAL(10,2) NOT NULL,
  size ENUM('small', 'medium', 'large', 'extra_large') NOT NULL,
  vegetarian BOOLEAN DEFAULT FALSE
);

CREATE TABLE orders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  customer_id INT NOT NULL,
  total_amount DECIMAL(10,2) NOT NULL,
  status ENUM('placed', 'preparing', 'out_for_delivery', 'delivered', 'cancelled') DEFAULT 'placed',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (customer_id) REFERENCES customers(id)
);

CREATE TABLE order_items (
  id INT AUTO_INCREMENT PRIMARY KEY,
  order_id INT NOT NULL,
  pizza_id INT NOT NULL,
  quantity INT NOT NULL DEFAULT 1,
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (pizza_id) REFERENCES pizzas(id)
);

4. Added Sample Data
INSERT INTO pizzas (name, description, price, size, vegetarian) VALUES
('Margherita', 'Classic tomato and cheese', 9.99, 'medium', TRUE),
('Pepperoni', 'Pepperoni and cheese', 12.99, 'medium', FALSE),
('Vegetarian', 'Bell peppers, onions, mushrooms, olives', 11.99, 'medium', TRUE);

INSERT INTO customers (name, email, phone, address) VALUES
('John Smith', 'john@example.com', '555-1234', '123 Main St'),
('Jane Doe', 'jane@example.com', '555-5678', '456 Oak Ave'),
('Bob Johnson', 'bob@example.com', '555-9012', '789 Pine Rd');

INSERT INTO orders (customer_id, total_amount, status) VALUES 
(1, 22.98, 'delivered'), 
(2, 11.99, 'preparing'), 
(3, 32.97, 'out_for_delivery');

INSERT INTO order_items (order_id, pizza_id, quantity) VALUES 
(1, 1, 1), 
(1, 2, 1), 
(2, 3, 1), 
(3, 2, 2), 
(3, 1, 1);

5. Endpoints
http://161.35.132.49:3000/
GET /api/pizzas - List all pizzas
GET /api/customers - List all customers
GET /api/orders - List all orders

6. Configured MySQL Authentication

Changed root user to use mysql_native_password authentication method for compatibility with XMysql

sqlALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'MicahLove08';
FLUSH PRIVILEGES;

7. Installed and Ran XMysql
bashsudo npm install -g xmysql
xmysql -h localhost -u root -p MicahLove08 -d pizza_delivery -r 0.0.0.0
