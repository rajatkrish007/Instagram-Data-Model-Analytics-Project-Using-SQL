# Instagram Data Model & Analytics Project
#
## Overview

This project involves designing a data model for Instagram using PostgreSQL. It demonstrates how to create tables, generate SQL statements, insert and update data, and perform analytics using SQL. The project covers a wide range of SQL concepts, including aggregation functions, subqueries, window functions, Common Table Expressions (CTEs), case statements, and date manipulation.
#
## Table of Contents

- [Overview](#overview)
- [Data Model Design](#data-model-design)
  - [Users Table](#users-table)
  - [Posts Table](#posts-table)
  - [Comments Table](#comments-table)
  - [Likes Table](#likes-table)
  - [Followers Table](#followers-table)
- [SQL Queries](#sql-queries)
  - [Creating Tables](#creating-tables)
  - [Inserting Data](#inserting-data)
  - [Updating Data](#updating-data)
- [Analytics Examples](#analytics-examples)
  - [WHERE Condition](#where-condition)
  - [ORDER BY](#order-by)
  - [GROUP BY and HAVING](#group-by-and-having)
  - [Aggregation Functions](#aggregation-functions)
  - [Subqueries](#subqueries)
  - [Window Functions](#window-functions)
  - [Common Table Expressions (CTEs)](#common-table-expressions-ctes)
  - [Case Statements](#case-statements)
  - [Date Casting and Working with Dates](#date-casting-and-working-with-dates)
- [Screenshots](#screenshots)
- [Conclusion](#conclusion)
#
## Data Model Design

We created the following tables to model Instagram data:

### Users Table

| Column        | Type         | Constraints     |
| ------------- | ------------ | --------------- |
| user\_id      | SERIAL       | PRIMARY KEY     |
| name          | VARCHAR(50)  | NOT NULL        |
| email         | VARCHAR(100) | UNIQUE NOT NULL |
| phone\_number | VARCHAR(20)  | UNIQUE          |

### Posts Table

| Column      | Type         | Constraints                                                 |
| ----------- | ------------ | ----------------------------------------------------------- |
| post\_id    | SERIAL       | PRIMARY KEY                                                 |
| user\_id    | INTEGER      | NOT NULL, FOREIGN KEY (user\_id) REFERENCES Users(user\_id) |
| caption     | TEXT         |                                                             |
| image\_url  | VARCHAR(200) |                                                             |
| created\_at | TIMESTAMP    | DEFAULT CURRENT\_TIMESTAMP                                  |

### Comments Table

| Column        | Type      | Constraints                                                 |
| ------------- | --------- | ----------------------------------------------------------- |
| comment\_id   | SERIAL    | PRIMARY KEY                                                 |
| post\_id      | INTEGER   | NOT NULL, FOREIGN KEY (post\_id) REFERENCES Posts(post\_id) |
| user\_id      | INTEGER   | NOT NULL, FOREIGN KEY (user\_id) REFERENCES Users(user\_id) |
| comment\_text | TEXT      | NOT NULL                                                    |
| created\_at   | TIMESTAMP | DEFAULT CURRENT\_TIMESTAMP                                  |

### Likes Table

| Column      | Type      | Constraints                                                 |
| ----------- | --------- | ----------------------------------------------------------- |
| like\_id    | SERIAL    | PRIMARY KEY                                                 |
| post\_id    | INTEGER   | NOT NULL, FOREIGN KEY (post\_id) REFERENCES Posts(post\_id) |
| user\_id    | INTEGER   | NOT NULL, FOREIGN KEY (user\_id) REFERENCES Users(user\_id) |
| created\_at | TIMESTAMP | DEFAULT CURRENT\_TIMESTAMP                                  |

### Followers Table

| Column             | Type      | Constraints                                                           |
| ------------------ | --------- | --------------------------------------------------------------------- |
| follower\_id       | SERIAL    | PRIMARY KEY                                                           |
| user\_id           | INTEGER   | NOT NULL, FOREIGN KEY (user\_id) REFERENCES Users(user\_id)           |
| follower\_user\_id | INTEGER   | NOT NULL, FOREIGN KEY (follower\_user\_id) REFERENCES Users(user\_id) |
| created\_at        | TIMESTAMP | DEFAULT CURRENT\_TIMESTAMP                                            |
#
## SQL Queries

### Creating Tables

Below is the SQL code to create the tables:

```sql
CREATE TABLE Users (
    user_id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone_number VARCHAR(20) UNIQUE
);

CREATE TABLE Posts (
    post_id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL,
    caption TEXT,
    image_url VARCHAR(200),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

CREATE TABLE Comments (
    comment_id SERIAL PRIMARY KEY,
    post_id INTEGER NOT NULL,
    user_id INTEGER NOT NULL,
    comment_text TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES Posts(post_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

CREATE TABLE Likes (
    like_id SERIAL PRIMARY KEY,
    post_id INTEGER NOT NULL,
    user_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES Posts(post_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

CREATE TABLE Followers (
    follower_id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL,
    follower_user_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (follower_user_id) REFERENCES Users(user_id)
);
```


### Inserting Data

```sql
-- Inserting into Users table
INSERT INTO Users (name, email, phone_number)
VALUES
    ('John Smith', 'johnsmith@gmail.com', '1234567890'),
    ('Jane Doe', 'janedoe@yahoo.com', '0987654321'),
    ('Bob Johnson', 'bjohnson@gmail.com', '1112223333'),
    ('Alice Brown', 'abrown@yahoo.com', NULL),
    ('Mike Davis', 'mdavis@gmail.com', '5556667777');

-- Inserting into Posts table
INSERT INTO Posts (user_id, caption, image_url)
VALUES
    (1, 'Beautiful sunset', '<https://www.example.com/sunset.jpg>'),
    (2, 'My new puppy', '<https://www.example.com/puppy.jpg>'),
    (3, 'Delicious pizza', '<https://www.example.com/pizza.jpg>'),
    (4, 'Throwback to my vacation', '<https://www.example.com/vacation.jpg>'),
    (5, 'Amazing concert', '<https://www.example.com/concert.jpg>');
```


### Updating Data

```sql
-- Updating the caption of post_id 3
UPDATE Posts
SET caption = 'Best pizza ever'
WHERE post_id = 3;
```
#
## Analytics Examples

### WHERE Condition

```sql
-- Selecting all the posts where user_id is 1
SELECT *
FROM Posts
WHERE user_id = 1;
```

### ORDER BY

```sql
-- Selecting all the posts and ordering them by created_at in descending order
SELECT *
FROM Posts
ORDER BY created_at DESC;
```

### GROUP BY and HAVING

```sql
-- Counting the number of likes for each post and showing only the posts with more than 2 likes
SELECT Posts.post_id, COUNT(Likes.like_id) AS num_likes
FROM Posts
LEFT JOIN Likes ON Posts.post_id = Likes.post_id
GROUP BY Posts.post_id
HAVING COUNT(Likes.like_id) > 2;
```

### Aggregation Functions

```sql
-- Finding the total number of likes for all posts
SELECT SUM(num_likes) AS total_likes
FROM (
    SELECT COUNT(Likes.like_id) AS num_likes
    FROM Posts
    LEFT JOIN Likes ON Posts.post_id = Likes.post_id
    GROUP BY Posts.post_id
) AS likes_by_post;
```

### Subqueries

```sql
-- Finding all the users who have commented on post_id 1
SELECT name
FROM Users
WHERE user_id IN (
    SELECT user_id
    FROM Comments
    WHERE post_id = 1
);
```

### Window Functions

```sql
-- Ranking the posts based on the number of likes
SELECT post_id, num_likes, RANK() OVER (ORDER BY num_likes DESC) AS rank
FROM (
    SELECT Posts.post_id, COUNT(Likes.like_id) AS num_likes
    FROM Posts
    LEFT JOIN Likes ON Posts.post_id = Likes.post_id
    GROUP BY Posts.post_id
) AS likes_by_post;
```

### Common Table Expressions (CTEs)

```sql
-- Finding all the posts and their comments using a Common Table Expression (CTE)
WITH post_comments AS (
    SELECT Posts.post_id, Posts.caption, Comments.comment_text
    FROM Posts
    LEFT JOIN Comments ON Posts.post_id = Comments.post_id
)
SELECT *
FROM post_comments;
```

### Case Statements

```sql
-- Categorizing the posts based on the number of likes
SELECT
    post_id,
    CASE
        WHEN num_likes = 0 THEN 'No likes'
        WHEN num_likes < 5 THEN 'Few likes'
        WHEN num_likes < 10 THEN 'Some likes'
        ELSE 'Lots of likes'
    END AS like_category
FROM (
    SELECT Posts.post_id, COUNT(Likes.like_id) AS num_likes
    FROM Posts
    LEFT JOIN Likes ON Posts.post_id = Likes.post_id
    GROUP BY Posts.post_id
) AS likes_by_post;
```

### Date Casting and Working with Dates

```sql
-- Finding all the posts created in the last month
SELECT *
FROM Posts
WHERE created_at >= CAST(DATE_TRUNC('month', CURRENT_TIMESTAMP - INTERVAL '1 month') AS DATE);
```
#
## Data Model And Data Analysis

![Instagram Data Model and Analysis_page-0001](https://github.com/user-attachments/assets/57e36f1b-527e-4ded-bb82-6f5b677feb45)

![Create_Tables](https://github.com/user-attachments/assets/29478ac6-e46d-4172-a566-798f3a803a99)

![Inserting_Values](https://github.com/user-attachments/assets/62125d5a-59b4-41fc-b41b-6a314f66101f)

![Checking_Tables](https://github.com/user-attachments/assets/efb4fb9c-0efb-4005-9bd6-eb491c957d21)

![Updating_Captions](https://github.com/user-attachments/assets/61dc9001-095b-40d5-9411-2906a6f4f345)

![op1(select)](https://github.com/user-attachments/assets/06882584-853a-4dac-9901-1d6e89ebaba0)

![op2(select)](https://github.com/user-attachments/assets/c2d61700-32e7-42dc-b809-dbb389c9dece)

![op3(select)](https://github.com/user-attachments/assets/6fbbd213-5f4a-4cb2-9c4e-3c35142a7b8c)

![op4(select)](https://github.com/user-attachments/assets/4dffbd29-eea5-4c2d-8030-53ce788a7c4d)

![op5(select)](https://github.com/user-attachments/assets/6f884095-47e7-4c78-bb60-96cd7cd02f77)

![op6(select)](https://github.com/user-attachments/assets/60f9f41d-cddd-4342-bd04-9ed94448ae6e)

![op7(select)](https://github.com/user-attachments/assets/ef5d56b3-0e13-4e80-9a42-de3e2ba52b02)

![op8(select)](https://github.com/user-attachments/assets/c5ef64bb-b92f-40f1-b272-20038832f5bd)

![op9(select)](https://github.com/user-attachments/assets/f0a7ee66-c3d9-443c-9432-dfaf0192e83d)

![op10(select)](https://github.com/user-attachments/assets/e4872fdc-c7cd-4cdc-a627-5efccf9cb507)

![op11(select)](https://github.com/user-attachments/assets/27a8bb99-fd2a-4cd6-ba70-7ff7b7db19ad)

#
## Conclusion

In this project, we successfully designed and implemented a PostgreSQL data model for Instagram, covering key SQL concepts and techniques. We demonstrated the creation of various tables that represent Instagram data, including users, posts, comments, likes, and followers. Additionally, we executed several SQL operations such as creating tables, inserting and updating data, and performing complex analytics queries. Here are the key learning outcomes:

1. **Data Modeling with SQL**:  
   - Developed a relational database schema representing Instagram's core functionalities, including users, posts, comments, likes, and followers.
   - Designed tables with appropriate data types, constraints, and foreign key relationships to ensure data integrity.

2. **Advanced SQL Techniques**:  
   - Gained experience with advanced SQL concepts, including aggregation functions, subqueries, window functions, Common Table Expressions (CTEs), and case statements.
   - Learned to write complex queries to extract, manipulate, and analyze data efficiently.

3. **Data Insertion and Updates**:  
   - Learned how to insert large datasets into the database while adhering to data constraints (e.g., unique email addresses and phone numbers).
   - Explored how to update specific records in a table and track changes.

4. **Analytics and Reporting**:  
   - Practiced using SQL for data analysis, including grouping and aggregating data to generate meaningful insights (e.g., counting likes, ranking posts).
   - Applied the `WHERE`, `ORDER BY`, and `HAVING` clauses to filter and organize data for reporting.

5. **Date and Time Manipulation**:  
   - Gained proficiency in working with date and time functions, such as truncating timestamps and filtering posts based on date ranges (e.g., posts from the last month).

6. **Optimizing Queries with Window Functions and CTEs**:  
   - Leveraged window functions to rank data dynamically and used CTEs for cleaner, more readable queries when working with complex joins.

7. **Problem-Solving and SQL Optimization**:  
   - Developed a deeper understanding of how to structure SQL queries for optimal performance and readability while handling large datasets.

Through this project, we have built a solid foundation in SQL and data modeling, which are crucial skills for working with relational databases and performing data analysis in real-world applications.


Gained proficiency in working with date and time functions, such as truncating timestamps and filtering posts based on date ranges (e.g., posts from the last month).
Optimizing Queries with Window Functions and CTEs:

Leveraged window functions to rank data dynamically and used CTEs for cleaner, more readable queries when working with complex joins.
Problem-Solving and SQL Optimization:

Developed a deeper understanding of how to structure SQL queries for optimal performance and readability while handling large datasets.
