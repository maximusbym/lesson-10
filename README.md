# lesson-10

##MySQL commands to remember:

`CREATE DATABASE...`
`ALTER DATABASE...`
`DROP DATABASE...`

`CREATE TABLE...`
`ALTER TABLE...`
`TRUNCATE TABLE...`
`DROP TABLE...`

`INSERT...`

`SELECT... (WHERE... ORDER BY... LIMIT...)`

`UPDATE... (WHERE... ORDER BY... LIMIT...)`

`DELETE... (WHERE... ORDER BY... LIMIT...)`

##Joins 

`...LEFT JOIN...` (same as `...LEFT OUTER JOIN...`)

```
SELECT products.`id`, products.`title`, categories.`id`, categories.`title`  
FROM `products` 
LEFT JOIN `categories` ON `categories`.`id` = `products`.`category_id`
```

```
SELECT u.name, u.`email`, u.`age`, g.`name`  
FROM `users` as u 
LEFT JOIN `geonames_ua` as g ON u.`city_id` = g.`id`
WHERE g.`feature2` IN ('PPLC','PPLA','PPLA2','PPLA3','PPLA4') 
	AND u.`email` LIKE '%@gmail.com'
	AND u.`age` BETWEEN 25 AND 55
ORDER BY g.`population` DESC
LIMIT 10
```

```
SELECT u.name, u.`email`, u.`age`, g.`name`  
FROM `users` as u 
LEFT JOIN `geonames_ua` as g ON u.`city_id` = g.`id`
WHERE g.`id` IS NULL
LIMIT 10
```

`...RIGHT JOIN...` (same as `...RIGHT OUTER JOIN...`)

```
SELECT products.`id`, products.`title`, categories.`id`, categories.`title`  
FROM `products` 
RIGHT JOIN `categories` ON `categories`.`id` = `products`.`category_id`
WHERE `products`.`id` IS NULL
```

`...CROSS JOIN...`

```
SELECT c1.`id`, c1.`title`, c2.`id`, c2.`title`  
FROM `categories` c1 
CROSS JOIN `categories` c2
```


`...INNER JOIN...`

```
SELECT products.`id`, products.`title`, categories.`id`, categories.`title`  
FROM `products` 
INNER JOIN `categories` ON `categories`.`id` = `products`.`category_id`
```

```
SELECT u.name, u.`email`, u.`age`, g.`name`  
FROM `users` as u 
INNER JOIN `geonames_ua` as g ON u.`city_id` = g.`id`
WHERE g.`id` IS NULL
LIMIT 10
```

##Unions

`...UNION...`
```
(SELECT * FROM `categories`)
UNION 
(SELECT * FROM `special_categories`)
```


##Indexes 

```
SHOW INDEXES IN `users`
```

```
DROP INDEX email_age ON `users`
```

`PRIMARY KEY` 

`UNIQUE` 

`INDEX`

```
CREATE INDEX feature2 ON `geonames_ua`(`feature2`);
```

`Composite Indexes` (up to 16 columns)

```
CREATE INDEX age_email_id_name ON `users`(`age`,`email`,`id`,`name`); 
```

`FULLTEXT`

`Spatial Indexes`

```
SELECT u.`id`, u.`name`, u.`email`, u.`age`
FROM `users` as u 
WHERE u.`age` BETWEEN 25 AND 55
	AND u.`email` LIKE '%@gmail.com'
ORDER BY u.`age` DESC
LIMIT 50
```

```
SELECT u.`id`, u.`name`, u.`email`, u.`age`, g.`id`, g.`name` 
FROM `users` as u 
LEFT JOIN `geonames_ua` as g ON u.`city_id` = g.`id`
WHERE g.`feature2` IN ('PPLC','PPLA','PPLA2','PPLA3','PPLA4') 
	AND u.`email` LIKE '%@gmail.com'
	AND u.`age` BETWEEN 25 AND 55
ORDER BY u.`age` DESC
LIMIT 50
```

`EXPLAIN...`
http://www.slideshare.net/billkarwin/how-to-design-indexes-really (Star system - slide #39)

##Loading big Databases

MySQL command: ``LOAD DATA INFILE 'UA.txt' INTO TABLE `lesson9`.`geonames_ua`;``

http://dev.mysql.com/doc/refman/5.7/en/load-data.html


CLI command: ``mysqlimport -h host -u username -ppassword --fields-terminated-by='\t' --lines-terminated-by='\n' --columns='geonameid, name, ansiname, alternames, latitude, longitude, feature_class, feature_code, country_code, cc2, admin1_code, admin2_code, population, elevation, gtopo30, timezone, modification_date' --local database /path/geonames.txt``

http://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html

* DataBase is used: http://www.geonames.org/
http://download.geonames.org/export/dump/
http://www.geonames.org/export/codes.html



##Groups & Agrigate Functions

`...GROUP BY...(HAVING...)`

```
SELECT `name` FROM `users` GROUP BY `name`
```

`...COUNT()...`

```
SELECT g.`id`, g.`name`, u.`users_from_city`, u.`users_ids` 
FROM `geonames_ua` g 
INNER JOIN (
    SELECT city_id, COUNT(*) as `users_from_city`, GROUP_CONCAT(`id` ORDER BY `id` SEPARATOR',') as users_ids
    FROM `users`
	GROUP BY city_id
    HAVING city_id IS NOT NULL
) as u ON u.`city_id` = g.`id`
ORDER BY u.`users_from_city` DESC
```
`* index added on u.city_id`

`...AVG()...`

```
SELECT AVG(age) FROM users
```

`...MAX()...`
`...MIN()...`
`...SUM()...`

```
SELECT MAX(`price`) as max_price, MIN(`price`) as min_price, SUM(`price`) as total_sum FROM products
WHERE category_id IN ( SELECT id FROM categories WHERE title LIKE 'food' )
```

`...DISTINCT...` (works as GROUP BY)

```
SELECT DISTINCT `name` FROM `users`
```

##Subqueries

```
SELECT * FROM users WHERE city_id IN (SELECT id FROM geonames_ua)
```
it's the same as:
```
SELECT users.* FROM users,geonames_ua WHERE geonames_ua.id=users.city_id
```

##Stored Procedures & Functions

```
delimiter //

	DROP PROCEDURE IF EXISTS `findUser`//
	CREATE PROCEDURE findUser (IN userName VARCHAR(255))
	BEGIN
		SELECT * FROM users WHERE name = userName;
	END//

	DROP PROCEDURE IF EXISTS `renameCategory`//
	CREATE PROCEDURE renameCategory (IN catId INT, IN newName VARCHAR(255))
	BEGIN
		UPDATE categories SET title = newName WHERE id = catId;
		SELECT * FROM categories WHERE id = catId;
	END//

delimiter ;
```

```
CALL findUser('Misha');
```
```
CALL renameCategory(1, 'Nuts');
```

`Functions are computed values and cannot perform permanent environmental changes to SQL Server (i.e. no INSERT or UPDATE statements allowed).`<br/>
`A Function can be used inline in SQL Statements if it returns a scalar value or can be joined upon if it returns a result set.`

```
CREATE FUNCTION hello (s CHAR(20))
RETURNS CHAR(50) DETERMINISTIC
RETURN CONCAT('Hello, ',s,'!');
```

```
SELECT hello('world');
```

##Views

```
CREATE VIEW `user_city.v` AS SELECT users.name, users.email, geonames_ua.name as city_name FROM users, geonames_ua WHERE users.city_id = geonames_ua.id;
```

##Triggers

`Triggers are triggered with INSERT, UPDATE or DELETE.`

```
delimiter |

CREATE TRIGGER updateProduct BEFORE UPDATE ON products
  FOR EACH ROW
  BEGIN
    SET NEW.counter = OLD.counter + 1;
  END;
|

delimiter ;
```

```
SHOW TRIGGERS;
```

```
DROP TRIGGER lesson9.updateCounter;
```