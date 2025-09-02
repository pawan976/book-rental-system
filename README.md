package model;

public class Book {
    private int id;
    private String title;
    private boolean isRented;

    public Book(int id, String title, boolean isRented) {
        this.id = id;
        this.title = title;
        this.isRented = isRented;
    }

    public int getId() { return id; }
    public String getTitle() { return title; }
    public boolean isRented() { return isRented; }
}
package dao;

import model.Book;
import java.sql.*;
import java.util.*;

public class BookDAO {
    private Connection conn;

    public BookDAO() throws Exception {
        Class.forName("com.mysql.cj.jdbc.Driver");
        conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/bookdb", "root", "your_password");
    }

    public List<Book> getAllBooks() throws SQLException {
        List<Book> books = new ArrayList<>();
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("SELECT * FROM books");
        while (rs.next()) {
            books.add(new Book(rs.getInt("id"), rs.getString("title"), rs.getBoolean("isRented")));
        }
        return books;
    }

    public void rentBook(int id) throws SQLException {
        PreparedStatement ps = conn.prepareStatement("UPDATE books SET isRented = true WHERE id = ?");
        ps.setInt(1, id);
        ps.executeUpdate();
    }
}
package servlet;

import dao.BookDAO;
import model.Book;
import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;
import java.io.IOException;
import java.util.List;

@WebServlet("/books")
public class BookServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
        try {
            BookDAO dao = new BookDAO();
            List<Book> books = dao.getAllBooks();
            req.setAttribute("books", books);
            req.getRequestDispatcher("rent.jsp").forward(req, res);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    protected void doPost(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
        int bookId = Integer.parseInt(req.getParameter("bookId"));
        try {
            BookDAO dao = new BookDAO();
            dao.rentBook(bookId);
            res.sendRedirect("books");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
<!DOCTYPE html>
<html>
<head>
  <title>Book Rental System</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Welcome to Book Rental System</h1>
  <a href="books">View Available Books</a>
</body>
</html>
<%@ page import="java.util.*, model.Book" %>
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
  <title>Rent a Book</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h2>Available Books</h2>
  <ul>
    <%
      List<Book> books = (List<Book>) request.getAttribute("books");
      for (Book book : books) {
        if (!book.isRented()) {
    %>
      <li>
        <%= book.getTitle() %>
        <form method="post" action="books">
          <input type="hidden" name="bookId" value="<%= book.getId() %>">
          <button type="submit">Rent</button>
        </form>
      </li>
    <%
        }
      }
    %>
  </ul>
</body>
</html>
body {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
  padding: 20px;
}

h1, h2 {
  color: #333;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 10px 0;
}

button {
  padding: 5px 10px;
  background-color: #007BFF;
  color: white;
  border: none;
  cursor: pointer;
}
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" version="3.1">
  <servlet>
    <servlet-name>BookServlet</servlet-name>
    <servlet-class>servlet.BookServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>BookServlet</servlet-name>
    <url-pattern>/books</url-pattern>
  </servlet-mapping>
</web-app>
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
