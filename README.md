const express = require("express");
const mysql = require("mysql2");
const cors = require("cors");
const app = express();
app.use(express.json());
app.use(cors());

// MySQL database connection
const connection = mysql.createConnection({
  host: "localhost",
  user: "root",
  password: "Joliepher12345",
  database: "movies",
});

// Connect to the database
connection.connect((err) => {
  if (err) {
    console.error("Database connection failed: ", err.message);
    return;
  }
  console.log("Database connection success!");
});

// API Endpoints
// 1. Get all movies
app.get("/movies", (req, res) => {
  connection.query("SELECT * FROM movies", (error, results) => {
    if (error) {
      return res.status(500).json({ error: "Failed to fetch movies." });
    }
    res.json(results);
  });
});

// 2. Get a single movie by ID
app.get("/movies/:id", (req, res) => {
  const { id } = req.params;
  connection.query("SELECT * FROM movies WHERE id = ?", [id], (error, results) => {
    if (error) {
      return res.status(500).json({ error: "Failed to fetch movie." });
    }
    if (results.length === 0) {
      return res.status(404).json({ message: "Movie not found." });
    }
    res.json(results[0]);
  });
});

// 3. Create a new movie
app.post("/movies", (req, res) => {
  const { title, director, year, genre } = req.body;
  if (!title || !director || !year || !genre) {
    return res.status(400).json({ error: "All fields are required." });
  }
  
  const newMovie = { title, director, year, genre };
  connection.query("INSERT INTO movies SET ?", newMovie, (error, results) => {
    if (error) {
      return res.status(500).json({ error: "Failed to create movie." });
    }
    res.status(201).json({ id: results.insertId, ...newMovie });
  });
});

// 4. Update an existing movie by ID
app.put("/movies/:id", (req, res) => {
  const { id } = req.params;
  const { title, director, year, genre } = req.body;

  connection.query("UPDATE movies SET ? WHERE id = ?", [req.body, id], (error, results) => {
    if (error) {
      return res.status(500).json({ error: "Failed to update movie." });
    }
    if (results.affectedRows === 0) {
      return res.status(404).json({ message: "Movie not found." });
    }
    res.json({ message: "Movie updated successfully." });
  });
});

// 5. Delete a movie by ID
app.delete("/movies/:id", (req, res) => {
  const { id } = req.params;

  connection.query("DELETE FROM movies WHERE id = ?", [id], (error, results) => {
    if (error) {
      return res.status(500).json({ error: "Failed to delete movie." });
    }
    if (results.affectedRows === 0) {
      return res.status(404).json({ message: "Movie not found." });
    }
    res.json({ message: "Movie deleted successfully." });
  });
});

// Start the server
const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server is listening at port ${PORT}.`);
});


CREATE DATABASE movies;

USE movies;

CREATE TABLE movies (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  director VARCHAR(255) NOT NULL,
  year INT NOT NULL,
  genre VARCHAR(100) NOT NULL
);

INSERT INTO movies (title, director, year, genre) 
VALUES ('Inception', 'Christopher Nolan', 2010, 'Sci-Fi'),
       ('The Godfather', 'Francis Ford Coppola', 1972, 'Crime'),
       ('The Dark Knight', 'Christopher Nolan', 2008, 'Action');
SELECT * FROM movies;
UPDATE movies 
SET year = 2011 
WHERE title = 'Inception';
DELETE FROM movies 
WHERE title = 'The Godfather';

USE movies;
SHOW TABLES;
SHOW DATABASES;

