# SQL

Table `users`:  
|id|name|country|number|joined|
|:---:|:---:|:---:|:---:|:---:|
|1|Fati|Spain|22|2022-09-01 08:44:42|
|2|Puyol|Spain|5|2001-09-02 18:43:05|
|3|Alves|Brazil|8|2007-02-05 08:42:35|
|4|Pedro|Spain|9|2009-10-19 11:32:45|
|5|Gavi|Spain|30|2020-01-22 08:36:38|
|6|Xavi|Spain|6|2005-07-26 18:08:45|

Table `admins`:  
|id|name|country|
|:---:|:---:|:---:|
|1|Pep|Spain|
|2|Jose|Portugal|
|3|Fergie|Scotland|

Table `countries`:
|id|country|rank|
|:---:|:---:|:---:|
|1|Spain|2|
|2|Portugal|13|
|3|Scotland|89|
|4|Brazil|3|

> No data is normalized. Contrived examples.

## Select
```sql 
--All entries
select * from users

--No duplicates, distinct
select distinct country from users

--Particular columns
select id, name from users

--Basic where
select * from users where number < 10 and country = 'Spain'

--String wildcards: % any number of any chars, _ any char
select * from users where country = '%a%' --Any country with the word "a"
select * from users where name = '_avi' --Matches Gavi and Xavi

--Where value is IN a list
select * from users where number in (5,6,7,8)

--Process and display a column based on conditions
select name, 
    case
        where country = 'Spain' then 'Spanish player'
        else 'Non-Spanish player'
    end as is_spanish_national
from users

--Time can be compared the same way numbers are. Format: YYYY-MM-DD HH:MM:SS
select name from users where joined < '2010-00-00'
select name from users where joined < '2010-00-00 00:00:00'

--Order by a column
select name from users order by name

--Select only top x
select name from users order by joined limit 5

--Select top x but skip some entries, offset
select name from users order by joined limit 5 offset 3

--Combine entries from multiple tables
select country from users
    union
        select country from admins

--Combine and retain duplicates
select country from users
    union all
        select country from admins

--Aggregate: find oldest join date
select min(joined) from users

--Find user details with oldest join date
select * from users where joined = (
  select min(joined) from users
) 

--Find user details with latest join date
select * from users order by joindate desc limit 1
```

## Joins
```sql 
--Show details of admin countries
select * from countries
inner join admins on admins.country=countries.country

```

## References
[PGExercises](https://pgexercises.com)  
