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

`...LEFT JOIN...`

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

`...RIGHT JOIN...`

```
SELECT products.`id`, products.`title`, categories.`id`, categories.`title`  
FROM `products` 
RIGHT JOIN `categories` ON `categories`.`id` = `products`.`category_id`
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

`...OUTER JOIN...`

```
SELECT products.`id`, products.`title`, categories.`id`, categories.`title`  
FROM `products` 
LEFT OUTER JOIN `categories` ON `categories`.`id` = `products`.`category_id`
```

```
SELECT u.name, u.`email`, u.`age`, g.`name`  
FROM `users` as u 
LEFT OUTER JOIN `geonames_ua` as g ON u.`city_id` = g.`id`
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
`...AVG()...`
`...COUNT()...`
`...MAX()...`
`...MIN()...`
`...SUM()...`

`...DISTINCT...`

##Storage Procedures

##Views
