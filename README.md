Here is the markdown file with only cricket-related content:

# IPL Data Analysis

## Top 5 Batsmen for Each Team (Ranked Descending)

```sql
SELECT * 
FROM (
    SELECT 
        battingteam,
        batter,
        SUM(batsman_run) AS rn,
        DENSE_RANK() OVER (PARTITION BY battingteam ORDER BY rn DESC) AS rnk
    FROM ipl
    GROUP BY battingteam, batter
) t
WHERE t.rnk < 6
ORDER BY t.battingteam, t.rnk;
```

## V. Kohli Rolling Average

```sql
SELECT * 
FROM (
    SELECT 
        CONCAT("Match-", CAST(ROW_NUMBER() OVER (ORDER BY ID) AS CHAR)) AS 'match_no',
        SUM(batsman_run) AS 'runs_scored',
        SUM(SUM(batsman_run)) OVER w AS 'career_runs',
        AVG(SUM(batsman_run)) OVER w AS 'career_avg',
        AVG(SUM(batsman_run)) OVER (ROWS BETWEEN 9 PRECEDING AND CURRENT ROW) AS 'rolling_avg'
    FROM ipl
    WHERE batter = 'V Kohli'
    GROUP BY ID
    WINDOW w AS (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)
) t;
```

## Cumulative Runs Over Matches

```sql
SELECT 
    ROW_NUMBER() OVER (ORDER BY id) AS match_num,
    SUM(batsman_run) AS match_runs,
    SUM(SUM(batsman_run)) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cr_run
FROM data
WHERE batter = 'V Kohli'
GROUP BY id;
```

## Top 5 Bowlers by Wickets

```sql
SELECT * 
FROM (
    SELECT 
        bowlingteam,
        bowler,
        SUM(is_wicket) AS wickets
    FROM ipl
    GROUP BY bowlingteam, bowler
) t
WHERE t.rnk < 6
ORDER BY t.bowlingteam, t.rnk;
```

## Cumulative Runs for Specific Batsmen

```sql
SELECT 
    SUM(batsman_run),
    ROW_NUMBER() OVER (ORDER BY ID) AS mt_num,
    SUM(SUM(batsman_run)) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cum_run
FROM data
WHERE batter IN ('SA Yadav', 'SA Yadav')
GROUP BY ID;
```

## V. Kohli Match-wise Runs with Averages

```sql
SELECT * 
FROM (
    SELECT
        ROW_NUMBER() OVER (ORDER BY ID) AS match_no,
        SUM(batsman_run),
        SUM(SUM(batsman_run)) OVER w AS cum_run,
        AVG(SUM(batsman_run)) OVER w AS avg_run,
        AVG(SUM(batsman_run)) OVER (ROWS BETWEEN 9 PRECEDING AND CURRENT ROW) AS rn_avg_run
    FROM data
    WHERE batter = 'V Kohli'
    GROUP BY ID
    WINDOW w AS (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)
) t;
-- WHERE t.match_no IN (50, 100, 200);
```