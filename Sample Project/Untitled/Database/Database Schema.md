```mysql
-- Create database
CREATE DATABASE store_management;
USE store_management;

-- Users table for authentication
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    full_name VARCHAR(100) NOT NULL,
    role ENUM('admin', 'employee') NOT NULL DEFAULT 'employee',
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Categories table
CREATE TABLE categories (
    category_id INT AUTO_INCREMENT PRIMARY KEY,
    category_name VARCHAR(50) NOT NULL UNIQUE,
    description TEXT
);

-- Products table
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    category_id INT,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    cost_price DECIMAL(10, 2) NOT NULL,
    quantity INT NOT NULL DEFAULT 0,
    reorder_level INT DEFAULT 10,
    FOREIGN KEY (category_id) REFERENCES categories(category_id) ON DELETE SET NULL
);

-- Customers table
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_name VARCHAR(100) NOT NULL,
    email VARCHAR(100),
    phone VARCHAR(20),
    address TEXT
);

-- Sales table
CREATE TABLE sales (
    sale_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    user_id INT NOT NULL,
    sale_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10, 2) NOT NULL,
    payment_method ENUM('cash', 'credit_card', 'debit_card', 'online') NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE SET NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Sale items table (for individual items in a sale)
CREATE TABLE sale_items (
    sale_item_id INT AUTO_INCREMENT PRIMARY KEY,
    sale_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price_each DECIMAL(10, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (sale_id) REFERENCES sales(sale_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- Suppliers table
CREATE TABLE suppliers (
    supplier_id INT AUTO_INCREMENT PRIMARY KEY,
    supplier_name VARCHAR(100) NOT NULL,
    contact_person VARCHAR(100),
    email VARCHAR(100),
    phone VARCHAR(20),
    address TEXT
);

-- Purchase orders table
CREATE TABLE purchases (
    purchase_id INT AUTO_INCREMENT PRIMARY KEY,
    supplier_id INT NOT NULL,
    user_id INT NOT NULL,
    purchase_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10, 2) NOT NULL,
    status ENUM('pending', 'received', 'cancelled') DEFAULT 'pending',
    FOREIGN KEY (supplier_id) REFERENCES suppliers(supplier_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Purchase items table
CREATE TABLE purchase_items (
    purchase_item_id INT AUTO_INCREMENT PRIMARY KEY,
    purchase_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price_each DECIMAL(10, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (purchase_id) REFERENCES purchases(purchase_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- Insert default admin user (password is 'admin123')
INSERT INTO users (username, password, full_name, role, email) 
VALUES ('admin', '$2y$10$fwknoIzST3blylHAZ82sN.RyQA7vL9lTdwJT8AXmxVrHBhTHpjlym', 'Admin User', 'admin', 'admin@example.com');

-- Insert sample categories
INSERT INTO categories (category_name, description) VALUES
('Electronics', 'Electronic devices and accessories'),
('Clothing', 'All types of clothing items'),
('Groceries', 'Food and household items'),
('Stationery', 'Office and school supplies');

-- Insert sample products
INSERT INTO products (product_name, category_id, description, price, cost_price, quantity, reorder_level) VALUES
('Laptop', 1, '15-inch laptop with 8GB RAM', 899.99, 700.00, 20, 5),
('Smartphone', 1, 'Latest model smartphone', 699.99, 500.00, 30, 10),
('T-shirt', 2, 'Cotton t-shirt, various colors', 19.99, 8.00, 100, 20),
('Jeans', 2, 'Denim jeans, various sizes', 45.99, 25.00, 50, 15),
('Rice (5kg)', 3, '5kg bag of white rice', 12.99, 8.50, 40, 15),
('Notebook', 4, 'Spiral-bound notebook, 100 pages', 4.99, 2.00, 200, 50);

-- Insert sample customers
INSERT INTO customers (customer_name, email, phone, address) VALUES
('John Doe', 'john@example.com', '555-1234', '123 Main St, Anytown'),
('Jane Smith', 'jane@example.com', '555-5678', '456 Oak Ave, Somewhere');

-- Insert sample suppliers
INSERT INTO suppliers (supplier_name, contact_person, email, phone, address) VALUES
('Tech Supplies Inc.', 'Mark Johnson', 'mark@techsupplies.com', '555-9876', '789 Tech Blvd, Techville'),
('Fashion Wholesalers', 'Sarah Brown', 'sarah@fashionwholesalers.com', '555-4321', '321 Fashion St, Styletown');

-- Create indexes for performance
CREATE INDEX idx_product_category ON products(category_id);
CREATE INDEX idx_sale_user ON sales(user_id);
CREATE INDEX idx_sale_customer ON sales(customer_id);
CREATE INDEX idx_purchase_supplier ON purchases(supplier_id);
```

