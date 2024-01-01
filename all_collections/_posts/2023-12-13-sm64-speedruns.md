---
layout: post
title: SM64 Speedruns - A Python & SQL Project
date: 2023-12-23
categories: ["python", "pandas", "sql"]
---

This project was made after getting into Python in November '23 (mostly utilizing the Pandas module). I also wanted to work on my SQL skills a bit so after doing research and writing notes I was able to come up with this project. You can [view it here.](https://github.com/ouchmode/SM64-Speedrun-Data).

The dataset isn't complex, but I wanted to use something fun and relatable to me since I used to indulge in speedrunning a couple of years. It doesn't deal with multiple tables so there aren't any JOINs to use, but I know how to use them (JOIN + Implicit JOIN). 

I added more than enough comments in the code while I was making this as a way to make sure I understand everything and keep notes.

Below are two examples of the questions I tested myself on with the dataset I used. Here, I utilized Jupyter Notebook's SQL Magic (preceeding SQL statements with "%%sql" directly in Python outputs a DataFrame with the result of the statement(s)).


**First Place Times (per category, per platform (N64, EMU, VC))**
======
*While working this problem, I utilized 'CASE' statements as a way to transpose the data. I needed some form of aggregation so I used GROUP_CONCAT. Since 

```sql
/*
There are other countries other than five listed below, but the players in those countries don't have a 
first, second or third place time.
*/

SELECT PLATFORM, CATEGORY, COUNTRY,
    GROUP_CONCAT(CASE WHEN PLACE = 1 THEN PLAYER_NAME END) as FIRST,
    GROUP_CONCAT(CASE WHEN PLACE = 2 THEN PLAYER_NAME END) as SECOND,
    GROUP_CONCAT(CASE WHEN PLACE = 3 THEN PLAYER_NAME END) as THIRD
FROM ALL_CAT_SPEEDRUNS
GROUP BY PLATFORM, CATEGORY, COUNTRY
HAVING PLACE IN ('1', '2', '3')
ORDER BY COUNTRY ASC;
```
**RESULT:**
| PLATFORM | CATEGORY | COUNTRY       | FIRST         | SECOND        | THIRD          |
| ---------|:--------:| -------------:| -------------:| -------------:| --------------:|
| EMU      | 120 Star  | Australia      | None          | Nebula        | None           |
| VC       | 0 Star    | Australia      | None          | None          | PhoenixAUS     |
| N64      | 0 Star    | Canada         | Suigi         | None          | None           |
| N64      | 1 Star    | Canada         | Suigi         | None          | None           |
| N64      | 16 Star   | Canada         | Suigi         | None          | None           |
| N64      | 70 Star   | Canada         | Suigi         | None          | None           |
| EMU      | 1 Star    | Germany        | Raisn         | None          | None           |
| EMU      | 120 Star  | Germany        | Raisn         | None          | None           |
| EMU      | 70 Star   | Germany        | Raisn         | None          | None           |
| VC       | 0 Star    | Germany        | None          | Finnii602     | None           |
| VC       | 1 Star    | Germany        | Finnii602     | Aleph64       | None           |
| VC       | 120 Star  | Germany        | Aleph64       | Finnii602     | None           |
| VC       | 16 Star   | Germany        | None          | None          | Finnii602      |
| VC       | 70 Star   | Germany        | Finnii602     | None          | Aleph64        |
| EMU      | 0 Star    | Italy          | None          | ZUMMI         | None           |
| EMU      | 1 Star    | Italy          | None          | None          | ZUMMI          |
| N64      | 0 Star    | Italy          | None          | None          | cjrokokomero   |
| N64      | 1 Star    | Italy          | None          | None          | cjrokokomero   |
| EMU      | 70 Star   | Japan          | None          | None          | taihou         |
| N64      | 120 Star  | Japan          | Karin         | None          | None           |
| N64      | 70 Star   | Japan          | None          | ikori_o       | None           |
| VC       | 70 Star   | Panama         | None          | RianDiCielo   | None           |
| EMU      | 120 Star  | Poland         | None          | None          | Sigotu         |
| EMU      | 70 Star   | Spain          | None          | Zhilkan       | None           |
| EMU      | 0 Star    | United States   | None          | None          | TriforceTK     |
| N64      | 1 Star    | United States   | Tag609        | None          | None           |
| N64      | 120 Star  | United States   | None          | None          | Liam           |
| N64      | 16 Star   | United States   | Slipperynip    | None          | None           |
| VC       | 120 Star  | United States   | None          | None          | Odme_          |
| VC       | 0 Star    | Uzbekistan      | CyanogenSm64  | None          | None           |
| VC       | 1 Star    | Uzbekistan      | None          | None          | CyanogenSm64   |
| EMU      | 0 Star    | nan             | DuckTapeDuck   | None          | None           |
| EMU      | 1 Star    | nan             | None          | DuckTapeDuck  | None           |
| N64      | 0 Star    | nan             | None          | KANNO         | None           |
| N64      | 120 Star  | nan             | None          | marlene       | None           |



---

**Who has held the N64 World Record in each category the longest?**
======
*Getting the DAYS_RECORD_HELD was a bit fun. I learned about julianday() and casting within SQL.*

```sql
/*
     0: Suigi
     1: Suigi
     16: Suigi
     70: Suigi
     120: Karin
*/

SELECT PLATFORM,PLAYER_NAME, CATEGORY, PLACE, RUN_TIME, SUBMISSION_DATE, 
    --Casting the rounded value of the difference between the current date and the submission date to an integer to get a whole number.
    CAST(ROUND(julianday('now') - julianday(SUBMISSION_DATE)) AS INTEGER) AS DAYS_RECORD_HELD
FROM ALL_CAT_SPEEDRUNS
GROUP BY CATEGORY, PLATFORM
HAVING PLACE == 1 AND PLATFORM == "N64"
ORDER BY CATEGORY DESC;
```
**RESULT:**
| PLATFORM | PLAYER_NAME | CATEGORY  | PLACE | RUN_TIME      | SUBMISSION_DATE | DAYS_RECORD_HELD |
| ---------|-------------|-----------|-------|---------------|------------------|------------------:|
| N64      | Suigi       | 70 Star   | 1     | 0:46:41       | 2023-06-17       | 198              |
| N64      | Suigi       | 16 Star   | 1     | 0:14:35.500   | 2023-03-23       | 284              |
| N64      | Karin       | 120 Star  | 1     | 1:36:48       | 2023-10-21       | 72               |
| N64      | Suigi       | 1 Star    | 1     | 0:06:57.580   | 2023-04-12       | 264              |
| N64      | Suigi       | 0 Star    | 1     | 0:06:16.600   | 2023-10-27       | 66               |
