# ipl data analysuisi

## top 5 batsman for each team rank desc

```sql
select * from (select 
battingteam,batter,sum(batsman_run) as rn,
dense_rank() over (partition by battingteam order by rn desc ) as rnk
from ipl
group by battingteam,batter
) t
where t.rnk<6
order by t.battingteam, t.rnk
```


## v kohli  cumm avg for kohli


```sql

SELECT * FROM (SELECT 
CONCAT("Match-",CAST(ROW_NUMBER() OVER(ORDER BY ID) AS CHAR)) AS 'match_no',
SUM(batsman_run) AS 'runs_scored',
SUM(SUM(batsman_run)) OVER w AS 'career_runs',
AVG(SUM(batsman_run)) OVER w AS 'career_avg',
AVG(SUM(batsman_run)) OVER(ROWS BETWEEN 9 PRECEDING AND CURRENT ROW) AS 'rolling_avg'
FROM ipl
WHERE batter = 'V Kohli'
GROUP BY ID
WINDOW w AS (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)) t

```

## cumm runs over match

```sql
select row_number() over(order by id) as match_num,
sum(batsman_run) match_runs,
sum(sum(batsman_run)) over(rows between unbounded preceding and current row) as cr_run
from data
where batter = 'V Kohli'

group by id

```
