# 1419A Notes

## SQL

**Data Types**

- Numeric: INT, FLOAT, REAL, DOUBLE
- Character Strings: CHAR(n), VARCHAR(n), CLOB(size)
  - CLOB(2MB) for large objects (documents/web pages)
- Bit Strings: BIT(n), BIT VARYING(n), BLOB
  - BLOB(20MB)  for images
- Boolean
- Dates: DATE, TIME, TIMESTAMP, TIME WITH TIME ZONE



**Creating Tables**

TWEET: <ID, Time, Text>

```sql
create table TWEET (
	ID INT,
    Time TIMESTAMP,
    Text VARCHAR(100)
);
```

**Inserting Tuples**

```sql
insert into TWEET values(
	38972,
    2019-01-01 12:34:56,
    "hey"
);
```

**Default values**

```sql
ID INT DEFAULT 0,
```

**Not null**

```sql
ID INT NOT NULL,
```

or

```sql
ID INT PRIMARY KEY,
```

- `PRIMARY KEY` enforces `NOT NULL` and uniqueness of value

**Foreign keys**

```sql
FOREIGN KEY	(Tweet) REFERENCES TWEET(ID)
```

**Delete**

```sql
DELETE FROM TWEET WHERE ID = "596208"
```

behavior when delete

```sql
FOREIGN KEY (Tweet) REFERENCES TWEET(ID) ON DELETE CASCADE,
FOREIGN KEY (Tweet) REFERENCES TWEET(ID) ON DELETE SET NULL,
FOREIGN KEY (Tweet) REFERENCES TWEET(ID) ON DELETE RESTRICT
```

The delete behavior of the original key will also affect the foreign keys that reference the original key

***

**Basic Template**

```sql
SELECT <attribute list>
FROM <table list>
[ WHERE <condition> ]
[ GROUP BY <attribute list> ]
[ HAVING <condition> ]
[ ORDER BY <attribute list> ];
```

**Relational Algebra** 

- $\sigma$ selection:

  ```sql
  SELECT *
  FROM TWEET
  ```

- $\pi$ projection:

  ```sql
  WHERE <condition>
  ```

- Cross product: select from two features without condition

  ```sql
  SELECT ID, Text
  FROM Tweet, Author
  ```

- Join condition: select from two features with conditions

  ```sql
  SELECT ID, Text
  FROM Tweet, Author
  WHERE ID = Tweet
  ```

  aliasing to avoid ambiguity

  ```sql
  SELECT ID, Text
  FROM Tweet AS t,
  	 Author AS a
  WHERE t.ID = a.Tweet
  ```



**Join**

```sql
SELECT ID, Text
FROM (TWEET JOIN AUTHOR ON ID = TWeet)
WHERE Person = "w"
```

- `JOIN`: inner joint, delete all entries that don't satisfy the condition
- `LEFT OUTER JOIN`: left outer join, keep all entries of the left table that don't satisfy the condition
- `RIGHT OUTER JOIN`: right outer join
- `FULL OUTER JOIN`: full outer join

**Natural Join**

Natural join: assume condition is ALL PAIRS of attributes with matching names

- if no matches, form full cross product

```sql
SELECT ID, Text
FROM (TWEET NATURAL JOIN AUTHOR)
WHERE PERSON = "w"
```

Can perform inner join by setting the same feature id

```sql
SELECT ID, Text
FROM (TWEET AS t(tweetid, text) NATURAL JOIN
      AUTHOR AS a(person, tweetid))
```



**Group by**

```sql
SELECT Text,
Count(*), AVG(Likes)
FROM Tweet
GROUP By Text
```

- `SUM, MIN, MAX, COUNT, AVG`

- `HAVING`:

  ```sql
  HAVING COUNT(*) > 1
  ```

- `LIKE`:

  ```sql
  WHERE Text LIKE '%1951A%'
  ```

  