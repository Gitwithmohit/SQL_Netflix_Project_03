# SQL_Netflix_Project_03
This project uses SQL to perform an in-depth analysis of a Netflix movies and TV shows dataset. By writing and executing various queries, we aim to uncover key trends and patterns in content distribution, ratings, and genre popularity. This README documents the core business questions and the SQL solutions used to derive meaningful insights.
![Netflix](PICTURE.png)

# Overview

This project involves a comprehensive analysis of Netflix\'s movies and
TV shows data using SQL. The primary goal is to extract valuable
insights and answer various business questions based on the dataset.
This README provides a detailed account of the project\'s objectives,
business problems, solutions, findings, and conclusions.

# Objectives

\- Analyze the distribution of content types (movies vs. TV shows).

\- Identify the most common ratings for movies and TV shows.

\- List and analyze content based on release years, countries, and
durations.

\- Explore and categorize content based on specific criteria and
keywords.

# Dataset

The data for this project is sourced from a public Kaggle dataset.

# Schema
```sql
DROP TABLE IF EXISTS netflix;\
\
CREATE TABLE netflix\
(\
show_id VARCHAR(5),\
type VARCHAR(10),\
title VARCHAR(250),\
director VARCHAR(550),\
casts VARCHAR(1050),\
country VARCHAR(550),\
date_added VARCHAR(55),\
release_year INT,\
rating VARCHAR(15),\
duration VARCHAR(15),\
listed_in VARCHAR(250),\
description VARCHAR(550)\
);
```
# Business Problems and Solutions

## Count the Number of Movies vs. TV Shows

Objective: Determine the distribution of content types on Netflix.
```sql
SELECT type, COUNT(\*) FROM netflix GROUP BY 1;
```
## Find the Most Common Rating for Movies and TV Shows

Objective: Identify the most frequently occurring rating for each type
of content.
```sql
WITH RatingCounts AS (\
SELECT type, rating, COUNT(\*) AS rating_count\
FROM netflix\
GROUP BY type, rating\
),\
RankedRatings AS (\
SELECT type, rating, rating_count,\
RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank\
FROM RatingCounts\
)\
SELECT type, rating AS most_frequent_rating\
FROM RankedRatings\
WHERE rank = 1;
```

## List All Movies Released in a Specific Year (e.g., 2020)

Objective: Retrieve all movies released in a specific year.
```sql
SELECT \* FROM netflix WHERE release_year = 2020;
```
## Find the Top 5 Countries with the Most Content on Netflix

Objective: Identify the top 5 countries with the highest number of
content items.
```sql
SELECT \* FROM (\
SELECT UNNEST(STRING_TO_ARRAY(country, \',\')) AS country, COUNT(\*) AS
total_content\
FROM netflix\
GROUP BY 1\
) AS t1\
WHERE country IS NOT NULL\
ORDER BY total_content DESC\
LIMIT 5;
```
## Identify the Longest Movie

Objective: Find the movie with the longest duration.
```sql
SELECT \* FROM netflix WHERE type = \'Movie\' ORDER BY
SPLIT_PART(duration, \' \', 1)::INT DESC;
```
## Find Content Added in the Last 5 Years

Objective: Retrieve content added to Netflix in the last 5 years.
```sql
SELECT \* FROM netflix WHERE TO_DATE(date_added, \'Month DD, YYYY\') \>=
CURRENT_DATE - INTERVAL \'5 years\';
```
## Find All Movies/TV Shows by Director \'Rajiv Chilaka\'

Objective: List all content directed by \'Rajiv Chilaka\'.
```sql
SELECT \* FROM (\
SELECT \*, UNNEST(STRING_TO_ARRAY(director, \',\')) AS director_name\
FROM netflix\
) AS t WHERE director_name = \'Rajiv Chilaka\';
```
## List All TV Shows with More Than 5 Seasons

Objective: Identify TV shows with more than 5 seasons.
```sql
SELECT \* FROM netflix WHERE type = \'TV Show\' AND SPLIT_PART(duration,
\' \', 1)::INT \> 5;
```
## Count the Number of Content Items in Each Genre

Objective: Count the number of content items in each genre.
```sql
SELECT UNNEST(STRING_TO_ARRAY(listed_in, \',\')) AS genre, COUNT(\*) AS
total_content\
FROM netflix GROUP BY 1;
```
## Find each year and the average numbers of content release in India

Objective: Return top 5 years with highest avg content release.
```sql
SELECT country, release_year, COUNT(show_id) AS total_release,\
ROUND(COUNT(show_id)::numeric /\
(SELECT COUNT(show_id) FROM netflix WHERE country = \'India\')::numeric
\* 100, 2) AS avg_release\
FROM netflix\
WHERE country = \'India\'\
GROUP BY country, release_year\
ORDER BY avg_release DESC\
LIMIT 5;
```
# Findings and Conclusion

Based on this analysis, we can draw the following conclusions:\
\
Content Distribution: The dataset provides a diverse range of movies and
TV shows with varying ratings and genres, indicating a broad content
strategy.\
\
Common Ratings: Insights into the most common ratings give us an
understanding of the content\'s target audience.\
\
Geographical Insights: The top countries with the most content and the
average content releases in India highlight regional content
distribution patterns.\
\
Content Categorization: Categorizing content based on specific keywords
(e.g., in the description column) helps to understand the nature of
content available on the platform.\
\
This project demonstrates how SQL can be used to extract valuable,
actionable insights from raw data, which can inform content strategy and
decision-making for a company like Netflix.

## How to Use

1. **Clone the Repository**: Clone this repository to your local machine.
   ```sh
   [git clone https://github.com/Gitwithmohit/SQL_library_project_02.git](https://github.com/Gitwithmohit/SQL_Netflix_Project_03.git)
   ```

2. **Set Up the Database**: Execute the SQL scripts in the `Netflix_Business_Problems.sql` file to create and populate the database.
3. **Run the Queries**: Use the SQL queries in the `Netflix_Business_Problems.sql` file to perform the analysis.
4. **Explore and Modify**: Customize the queries as needed to explore different aspects of the data or answer additional questions.

## Author - MOHIT PURI


Thank you for your interest in this project!
