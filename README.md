# Netflix Movies and TV Shows Data Analysis using SQL
![Netflix Logo](https://github.com/chirag271/Netflix_SQL_Project/blob/main/logo.png)

## Objective

-- SCHEMAS of Netflix

DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
	show_id	VARCHAR(5),
	type    VARCHAR(10),
	title	VARCHAR(250),
	director VARCHAR(550),
	casts	VARCHAR(1050),
	country	VARCHAR(550),
	date_added	VARCHAR(55),
	release_year	INT,
	rating	VARCHAR(15),
	duration	VARCHAR(15),
	listed_in	VARCHAR(250),
	description VARCHAR(550)
);

SELECT * FROM netflix;

copy netflix
FROM 'D:/Excel Files/netflix_titles.csv'
DELIMITER ','
CSV HEADER;


-- 1. Count the number of Movies vs TV Shows
Ans: 

	 ```sql
	 SELECT type, COUNT(*) AS total_content
	 FROM netflix
	 GROUP BY type;
     ```
	 
-- 2. Find the most common rating for movies and TV shows
Ans: 
      ```sql
	  SELECT
 		 type,
		 rating
	 FROM
	 	(
 		SELECT
		 	type,
			rating,
			COUNT(*),
			RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) AS Ranking
		FROM netflix
		GROUP BY 1,2
		)AS t1
		WHERE ranking = 1;
        ```

-- 3. List all movies released in a specific year (e.g., 2020)
Ans: 

     ```sql
	 SELECT title FROM netflix
     WHERE release_year = '2020'
	 AND type = 'Movie';
     ```
	 
-- 4. Find the top 5 countries with the most content on Netflix
Ans:  
      
	  ```sql
	  SELECT
 			UNNEST(STRING_TO_ARRAY(country,',')) AS new_country,
			COUNT(show_id) AS total_content
      FROM netflix
	  GROUP BY 1
	  ORDER BY 2 DESC
	  LIMIT 5;
	  ```

-- 5. Identify the longest movie
Ans:  

      ```sql
	  SELECT title, duration FROM netflix
      WHERE type = 'Movie'
	  AND duration = (SELECT MAX(duration) FROM netflix);
	  ```

-- 6. Find content added in the last 5 years
Ans: 


     ```sql
	 SELECT * FROM netflix
     WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 YEARS';
     ```
	 
-- 7. Find all the movies/TV shows by director 'Rajiv Chilaka'!
Ans:  

      ```sql
	  SELECT type, title, director FROM netflix
      WHERE director = 'Rajiv Chilaka';
      ```
	  
-- 8. List all TV shows with more than 5 seasons
Ans: 
     
	 ```sql
	 SELECT * FROM netflix
	 WHERE type = 'TV Show'
	 AND CAST(SPLIT_PART(duration, ' ',1)AS INTEGER) > 5;
     ```

-- 9. Count the number of content items in each genre
Ans:  

     
	 ```sql
	 SELECT
			UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS Genre,
			COUNT(show_id) AS Total_Content
      FROM netflix
	  GROUP BY 1
	  ORDER BY Total_Content DESC;
      ```
	  

-- 10. Find each year and the average number of content releases in India on Netflix; return the top 5 years with the highest average content release!
Ans:  



       ```sql
	   SELECT
 			EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS Year,
			COUNT(*) AS Yearly_content,
			ROUND(COUNT(*):: numeric/(SELECT COUNT(*)FROM netflix WHERE country = 'India')::numeric * 100,2) AS avg_content_per_year
		FROM Netflix
		WHERE country = 'India'
		GROUP BY 1
		LIMIT 5;
        ```
		
-- 11. List all movies that are documentaries
Ans: 
     
	 ```sql
	 SELECT * FROM netflix
     WHERE type = 'Movie'
	 AND listed_in LIKE '%Documentaries%';
	 ```
	 
-- 12. Find all content without a director
Ans: 

     ```sql
	 SELECT * FROM netflix
     WHERE director IS NULL;
	 ```

-- 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!
 Ans:   
 
 
        ```sql
		SELECT * FROM netflix
        WHERE type = 'Movie'
	    AND TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '10 YEARS'
	    AND casts LIKE '%Salman Khan%';
        ```
		
-- 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.
Ans: 


     ```sql
	 SELECT
	 UNNEST(STRING_TO_ARRAY(casts, ',')) AS Actors,
	 COUNT(*) AS Total_content
	 FROM netflix
	 WHERE country ILIKE '%India'
	 GROUP BY 1
	 ORDER BY 2 DESC
	 LIMIT 10;
	 ```
	 
-- 15. Categorise the content based on the presence of the keywords 'kill' and 'violence' in 
-- 	the description field. Label content containing these keywords as 'Bad' and all other 
-- 	content as 'Good'. Count how many items fall into each category.
Ans:  


     ```sql
	 WITH new_table
      AS
	  (
	  SELECT
		description, 
			CASE
				WHEN 
				description ILIKE '%kill%' OR
				description ILIKE '%violence%' THEN 'Bad Content'
				ELSE 'Good Content'
			END Category
	  FROM netflix
	  )
	  SELECT category,
	  COUNT(*) AS Total_content
	  FROM new_table
	  GROUP BY 1;
	  ```
