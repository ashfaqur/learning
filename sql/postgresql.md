# PostgreSQL

## Commands:

Check Version:

    psql --version

My user login prompt

    psql -U ash -d ashdb

Prompt

    psql

HELP

    \h

Exit

    \q

List users / roles

    \du

List all databases

    \l

Schema for table

    \d tablename

## User

Switch to default user

    sudo -i -u postgres

Prompt

    psql


Delete user

    DROP ROLE <user>;

Create a new user

    CREATE ROLE <user> WITH LOGIN CREATEDB CREATEROLE PASSWORD '<password>';

Change password

    ALTER ROLE <user> WITH PASSWORD '<password>';

Create a database for user

    CREATE DATABASE <db_name> OWNER <user>;

Log in as new user with database

    psql -U <user> -d <database>


## Server

Check if postgresql server is running 

    sudo systemctl status postgresql

Start sever

    sudo systemctl start postgresql

Stop server

    sudo systemctl stop postgresql

Restart server

    sudo systemctl restart postgresql

Disable auto start

    sudo systemctl disable postgresql

Enable auto start

    sudo systemctl enable postgresql


## SQL

Table

```sql
    CREATE TABLE weather (
    city            varchar(80),
    temp_lo         int,           -- low temperature
    temp_hi         int,           -- high temperature
    prcp            real,          -- precipitation
    date            date
    );

    CREATE TABLE cities (
        name            varchar(80),
        location        point
    );

    DROP TABLE tablename;
```

Insert

```sql

    INSERT INTO weather (city, temp_lo, temp_hi, prcp, date)
    VALUES ('San Francisco', 43, 57, 0.0, '1994-11-29');

```

Select

```sql

    SELECT * FROM weather;

    SELECT city, temp_lo, temp_hi, prcp, date FROM weather;

    SELECT * FROM weather
    WHERE city = 'San Francisco' AND prcp > 0.0;
```

Order by

```sql

    SELECT * FROM weather
    ORDER BY city, temp_lo;
```

Distinct

```sql

    SELECT DISTINCT city
    FROM weather
    ORDER BY city;
```

Join

```sql

    SELECT * FROM weather JOIN cities ON city = name;

    SELECT weather.city, weather.temp_lo, weather.temp_hi,
       weather.prcp, weather.date, cities.location
    FROM weather JOIN cities ON weather.city = cities.name;

    SELECT max(temp_lo) FROM weather;
```

Where

```sql

    SELECT city FROM weather
    WHERE temp_lo = (SELECT max(temp_lo) FROM weather);
```

Group, functions

```sql

    SELECT city, count(*), max(temp_lo)
    FROM weather
    GROUP BY city;
```

Having

```sql

    SELECT city, count(*), max(temp_lo)
    FROM weather
    GROUP BY city
    HAVING max(temp_lo) < 40;
```
    
Filter

```sql

    SELECT 
        city,
        count(*) FILTER (WHERE temp_lo < 45),
        max(temp_lo)
    FROM weather
    GROUP BY city;
```

Update / Edit

```sql

    UPDATE weather
    SET temp_hi = temp_hi - 2,  temp_lo = temp_lo - 2
    WHERE date > '1994-11-28';

    SELECT * FROM weather 
    ORDER BY weather.city DESC, weather.temp_lo DESC;
```

Delete

```sql

    DELETE FROM weather WHERE city = 'Hayward';
```

View

```sql
    CREATE VIEW weatherview AS
    SELECT w.city, w.temp_lo, w.temp_hi, w.prcp, w.date, c.location
        FROM weather w
        FULL OUTER JOIN cities c ON w.city = c.name;

    DROP VIEW IF EXISTS weatherview;
```

Keys

```sql

    CREATE TABLE cities (
        name     varchar(80) primary key,
        location point
    );

    CREATE TABLE weather (
            city      varchar(80) references cities(name),
            temp_lo   int,
            temp_hi   int,
            prcp      real,
            date      date
    );
```

Exercise: Implement a query to get a list of all students
and how many courses each student is enrolled in.

```sql
    SELECT s.studentid, s.studentname, COUNT(c.courseid)
    FROM students s
    LEFT OUTER JOIN studentcourses c
        ON s.studentid = c.studentid
    GROUP BY s.studentid, s.studentname
    ORDER BY s.studentid ASC;
```

Excercise:
Implement a query to get a list of all teachers and how many
students they each teach. If a teacher teaches the same student
in two courses, you should double count the student. Sort the list
in descending order of the number of students a teacher teaches.

```sql
    SELECT s.courseid, COUNT(s.studentid) AS students
    FROM studentcourses s
    GROUP BY s.courseid;

    SELECT c.teacherid, SUM(s.students) AS students
    FROM courses c
    INNER JOIN (SELECT s.courseid, COUNT(s.studentid) AS students
        FROM studentcourses s
        GROUP BY s.courseid
    ) s 
        ON c.courseid = s.courseid
    GROUP BY c.teacherid
    ORDER BY students DESC
    ;

    SELECT t.teachername, f.students
    FROM teachers t
    INNER JOIN (
    SELECT c.teacherid, SUM(s.students) AS students
    FROM courses c
    INNER JOIN (SELECT s.courseid, COUNT(s.studentid) AS students
        FROM studentcourses s
        GROUP BY s.courseid
    ) s 
        ON c.courseid = s.courseid
    GROUP BY c.teacherid
    ORDER BY students DESC
    ) f
        ON t.teacherid = f.teacherid
    ORDER BY f.students DESC
    ;

```

Simplier version

```sql

    SELECT t.teachername, COUNT(s.studentid) AS students
    FROM teachers t
    LEFT JOIN courses c
        ON t.teacherid = c.teacherid
    LEFT JOIN studentcourses s
        ON s.courseid = c.courseid
    GROUP BY t.teachername
    ORDER BY students DESC
    ;

```


