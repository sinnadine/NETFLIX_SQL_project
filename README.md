# NETFLIX_SQL_project

![](https://github.com/sinnadine/NETFLIX_SQL_project/edit/main/README.md#:~:text=README.md-,logo,-.png)

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema
```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
	show_id	VARCHAR(6),  
	type VARCHAR(10),
	title	VARCHAR(150),
	director	VARCHAR(208),
	casts	VARCHAR(1000),
	country VARCHAR(150),
	date_added	VARCHAR(50),
	release_year	INT,
	rating	VARCHAR(10),
	duration	VARCHAR(15),
	listed_in	VARCHAR(100),
	description VARCHAR(250)
);
```

## Business Problems and Solutions

### 1. Count the number of Movies vs TV Shows

```sql
SELECT 
	type, 
	COUNT(*) as total_content
FROM netflix
GROUP BY type;
```

### 2. Find the most common rating for movies and TV shows

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
		RANK() OVER (PARTITION BY type ORDER BY COUNT(*) DESC) as ranking
		-- MAX(rating)
	FROM netflix
	GROUP BY 1, 2
) as t1
WHERE 
	ranking = 1;
```

### 3. List all movies released in a specific year (e.g., 2020)

```sql
SELECT * FROM netflix
WHERE type = 'Movie'
	AND 
	release_year = 2020;
```

### 4. Find the top 5 countries with the most content on Netflix

```sql
SELECT 
	TRIM(UNNEST(STRING_TO_ARRAY(country, ','))) as new_country,
	COUNT(show_id)as total_content		
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

### 5. Identify the longest movie

```sql
SELECT * FROM netflix
WHERE 
	type = 'Movie'
	AND 
	duration = (SELECT MAX(duration) FROM netflix);
```

### 6. Find content added in the last 5 years

```sql
SELECT * FROM netflix
WHERE 
	TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

### 7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

```sql
SELECT 
	*
FROM netflix
WHERE 
	director  ILIKE '%Rajiv Chilaka%';
```	

### 8. List all TV shows with more than 5 seasons

```sql
SELECT * FROM netflix
WHERE 
	type = 'TV Show'
	AND 
	SPLIT_PART(duration, ' ', 1):: numeric > 5;

--SELECT
--SPLIT_PART('Apple Banana Cherry', ' ', 1)
```

### 9. Count the number of content items in each genre

```sql
SELECT 
	UNNEST(STRING_TO_ARRAY(listed_in, ',')) as genre,
	COUNT(show_id) as total_content
FROM netflix
GROUP BY 1;
```

### 10. Find each year and the average numbers of content release in India on netflix. 
 return top 5 year with highest avg content release!

```sql
SELECT
	EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) as year,
	COUNT(*) as yearly_content,
	ROUND(
	COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100 , 
	2) as avg_content
FROM netflix
WHERE COUNTRY = 'India'
GROUP BY 1
ORDER BY 3 DESC
LIMIT 5;
```	

### 11. List all movies that are documentaries

```sql
SELECT 
	*
FROM netflix
WHERE type = 'Movie'
	AND listed_in ILIKE '%Documentaries'
```


### 12. Find all content without a director

```sql
SELECT * 
FROM netflix
WHERE 
	director IS NULL
```

### 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!

```sql
SELECT * FROM netflix
WHERE 
	casts ILIKE '%Salman Khan%'
	AND 
	release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10
```

### 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

```sql
SELECT 
	UNNEST(STRING_TO_ARRAY(casts, ',')) as actor,
	COUNT(*) as total_content
FROM netflix
WHERE country ILIKE '%India'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```

### 15. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.

```sql
WITH new_table
AS
(
SELECT
	*,
	CASE
		WHEN description ILIKE '%kill%' OR
		description ILIKE '%violence%' THEN 'Bad_content'
		ELSE 'Good_content'
	END category
FROM netflix
)

SELECT
	category,
	COUNT(*) as total_content
FROM new_table
GROUP BY 1
```

## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

## The purpose of the project

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!





