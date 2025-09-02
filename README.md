# 📚 Book Rental System

A simple Book Rental System built using Core Java (Servlets, JDBC), HTML/CSS for the frontend, and MySQL for the database. Designed to run in NetBeans with Apache Tomcat.

## 🚀 Features

- View available books
- Rent a book (updates status in database)
- Simple and clean UI
- Built with Servlets and JSP
- MySQL database integration

## 🧱 Tech Stack

- **Backend**: Java Servlets, JDBC
- **Frontend**: HTML, CSS, JSP
- **Database**: MySQL
- **IDE**: NetBeans
- **Server**: Apache Tomcat

## 🗃️ Database Setup

```sql
CREATE DATABASE bookdb;

USE bookdb;

CREATE TABLE books (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    isRented BOOLEAN DEFAULT FALSE
);

INSERT INTO books (title, isRented) VALUES
('The Alchemist', FALSE),
('1984', FALSE),
('To Kill a Mockingbird', FALSE),
('The Great Gatsby', FALSE);
