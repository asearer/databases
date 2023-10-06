Make sure you download the starter code and run the following:

```sh
  psql < movies.sql
  psql movies_db
```

In markdown, you can place a code block inside of three backticks (```) followed by the syntax highlighting you want, for example

\```sql

SELECT \* FROM users;

\```

Using the `movies_db` database, write the correct SQL queries for each of these tasks:

1.  The title of every movie.

\```sql

SELECT title
FROM movies;


\```



2.  All information on the G-rated movies.

\```sql

SELECT *
FROM movies
WHERE rating = 'G';


\```




3.  The title and release year of every movie, ordered with the
    oldest movie first.

\```sql

SELECT title, release_year
FROM movies
ORDER BY release_year;


\```


    
4.  All information on the 5 longest movies.

\```sql

SELECT *
FROM movies
ORDER BY duration_minutes DESC
LIMIT 5;


\```



5.  A query that returns the columns of `rating` and `total`, tabulating the
    total number of G, PG, PG-13, and R-rated movies.


\```sql

SELECT rating, COUNT(*) AS total
FROM movies
GROUP BY rating;


\```


6.  A table with columns of `release_year` and `average_runtime`,
    tabulating the average runtime by year for every movie in the database. The data should be in reverse chronological order (i.e. the most recent year should be first).


\```sql

SELECT release_year, AVG(duration_minutes) AS average_runtime
FROM movies
GROUP BY release_year
ORDER BY release_year DESC;


\```


7.  The movie title and studio name for every movie in the
    database.


\```sql

SELECT m.title, s.studio_name
FROM movies AS m
JOIN studios AS s ON m.studio_id = s.studio_id;


\```


8.  The star first name, star last name, and movie title for every
    matching movie and star pair in the database.

\```sql

SELECT s.first_name, s.last_name, m.title
FROM stars AS s
JOIN movie_stars AS ms ON s.star_id = ms.star_id
JOIN movies AS m ON ms.movie_id = m.movie_id;


\```



9.  The first and last names of every star who has been in a G-rated movie. The first and last name should appear only once for each star, even if they are in several G-rated movies. *IMPORTANT NOTE*: it's possible that there can be two *different* actors with the same name, so make sure your solution accounts for that.


\```sql

SELECT DISTINCT s.first_name, s.last_name
FROM stars AS s
INNER JOIN movie_stars AS ms ON s.star_id = ms.star_id
INNER JOIN movies AS m ON ms.movie_id = m.movie_id
WHERE m.rating = 'G';


\```


10. The first and last names of every star along with the number
    of movies they have been in, in descending order by the number of movies. (Similar to #9, make sure
    that two different actors with the same name are considered separately).


\```sql

SELECT s.first_name, s.last_name, COUNT(*) AS movie_count
FROM stars AS s
INNER JOIN movie_stars AS ms ON s.star_id = ms.star_id
GROUP BY s.first_name, s.last_name
ORDER BY movie_count DESC;


\```


### The rest of these are bonuses

11. The title of every movie along with the number of stars in
    that movie, in descending order by the number of stars.


\```sql

SELECT m.title, COUNT(ms.star_id) AS star_count
FROM movies AS m
LEFT JOIN movie_stars AS ms ON m.movie_id = ms.movie_id
GROUP BY m.title
ORDER BY star_count DESC;


\```


12. The first name, last name, and average runtime of the five
    stars whose movies have the longest average.

\```sql

SELECT s.first_name, s.last_name, AVG(m.duration_minutes) AS average_runtime
FROM stars AS s
JOIN movie_stars AS ms ON s.star_id = ms.star_id
JOIN movies AS m ON ms.movie_id = m.movie_id
GROUP BY s.first_name, s.last_name
ORDER BY average_runtime DESC
LIMIT 5;


\```



13. The first name, last name, and average runtime of the five
    stars whose movies have the longest average, among stars who have more than one movie in the database.

\```sql

SELECT s.first_name, s.last_name, AVG(m.duration_minutes) AS average_runtime
FROM stars AS s
JOIN (
  SELECT ms.star_id, AVG(m.duration_minutes) AS avg_runtime
  FROM movie_stars AS ms
  JOIN movies AS m ON ms.movie_id = m.movie_id
  GROUP BY ms.star_id
  HAVING COUNT(ms.movie_id) > 1
) AS subquery ON s.star_id = subquery.star_id
GROUP BY s.first_name, s.last_name
ORDER BY subquery.avg_runtime DESC
LIMIT 5;


\```



14. The titles of all movies that don't feature any stars in our
    database.

\```sql

SELECT m.title
FROM movies AS m
LEFT JOIN movie_stars AS ms ON m.movie_id = ms.movie_id
WHERE ms.star_id IS NULL;


\```



15. The first and last names of all stars that don't appear in any movies in our database.


\```sql

SELECT s.first_name, s.last_name
FROM stars AS s
LEFT JOIN movie_stars AS ms ON s.star_id = ms.star_id
WHERE ms.movie_id IS NULL;


\```


16. The first names, last names, and titles corresponding to every
    role in the database, along with every movie title that doesn't have a star, and the first and last names of every star not in a movie.

\```sql

-- First Query: Get first names, last names, and titles corresponding to every role
SELECT s.first_name, s.last_name, m.title
FROM stars AS s
JOIN movie_stars AS ms ON s.star_id = ms.star_id
JOIN movies AS m ON ms.movie_id = m.movie_id

UNION

-- Second Query: Get movie titles that don't have a star
SELECT m.title AS first_name, NULL AS last_name, NULL AS title
FROM movies AS m
LEFT JOIN movie_stars AS ms ON m.movie_id = ms.movie_id
WHERE ms.star_id IS NULL

UNION

-- Third Query: Get first and last names of stars not in a movie
SELECT s.first_name, s.last_name, NULL AS title
FROM stars AS s
LEFT JOIN movie_stars AS ms ON s.star_id = ms.star_id
WHERE ms.movie_id IS NULL;


\```


