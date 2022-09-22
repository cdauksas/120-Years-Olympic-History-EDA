# 120 Years of Olympic History EDA with Microsoft SQL Server #
### This is an exploratory data analysis on the modern olympic games from Athens 1896 to Rio 2016. ###
#### There are two csv files (athletes_events.csv and noc_regions.csv) used for this data set located [here on Kaggle](https://www.kaggle.com/datasets/heesoo37/120-years-of-olympic-history-athletes-and-results) ####

- Brief overview of the athelete_events table

```SQL
SELECT TOP 10 *
FROM PortfolioProject.dbo.athlete_events$
```
[](Photo1)

- Brief overview of the noc_regions table
```SQL
SELECT TOP 10 * 
FROM PortfolioProject.dbo.noc_regions$
```
[](Photo2)

- How many olympics games have been held so far?

```SQL
SELECT COUNT(Distinct games) as total_olympic_games
FROM PortfolioProject.dbo.athlete_events$
```

[](Photo3)

- What were the total number of nations that participated in each olympic games?

```SQL
with all_countries as
	(SELECT games, nr.region
	from PortfolioProject.dbo.athlete_events$ as oh
	join PortfolioProject.dbo.noc_regions$ as nr ON nr.noc = oh.noc
	group by games, nr.region)
select games, count(1) as total_countries
from all_countries
group by games
    order by games;
```

[](Photo4)

- Which year saw the highest and lowest number of countries participating in olympics?

```SQL

  with all_countries as
              (select games, nr.region
              from PortfolioProject.dbo.athlete_events$ oh
              join PortfolioProject.dbo.noc_regions$ nr ON nr.noc = oh.noc
              group by games, nr.region),
          tot_countries as
              (select games, count(1) as total_countries
              from all_countries
              group by games)
      select distinct
      concat(first_value(games) over(order by total_countries)
      , ' - '
      , first_value(total_countries) over(order by total_countries)) as Lowest_Countries,
      concat(first_value(games) over(order by total_countries desc)
      , ' - '
      , first_value(total_countries) over(order by total_countries desc)) as Highest_Countries
      from tot_countries
      order by 1;
      
  ```
    [](Photo5)
    
    - What sports were held in every summer olympics?
      - First, determine the total number of summer olympics
      - Second, get the unique sport for every summer olympic
      - Third, get the the count of every sport that competed in the summer olympic games
    ```SQL
   
	  With t1 as (
	  Select count(DISTINCT(Games)) as total_summer_games 
	  From PortfolioProject.dbo.athlete_events$
	  where season = 'summer' )
	  , 
	  t2 as (
	  SELECT distinct sport, Games
	  from PortfolioProject.dbo.athlete_events$
	   where season = 'summer' ) 
	   ,
	   t3 as (
	   SELECT sport, count(Games) as cnt
	   from t2
	   Group By sport
	   )
SELECT cnt, sport
From t3
where cnt = 29
    
    ```
 - These sports were played in all 29 summer olympic games
[](Photo6) 

 - What were the total sports played in each olympic game?
```SQL

	 With t1 as (
	  Select DISTINCT(Games), Sport
	  From PortfolioProject.dbo.athlete_events$), 
	  
	  t2 as (
	  Select Games, Count(1) as no_of_sports
	  From t1 
	  group by Games)
	   select * from t2
      order by no_of_sports desc;
```

[](Photo7)
