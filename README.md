# IPL Data Analysis

**Data Source:** [IPL Ball by Ball Data (2008-2022)](https://github.com/magaramol/ipl_sql/blob/main/DATA/IPL_Ball_by_Ball_2008_2022.csv)

## Top 5 Batsmen for Each Team (Ranked Descending)

This query retrieves the top 5 batsmen for each team, ranked by the total runs scored. The `DENSE_RANK` function is used to rank the batsmen within each team based on their total runs.

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

This query calculates V. Kohli's rolling average runs scored over his career. It shows the match number, runs scored in each match, cumulative runs, career average, and a rolling average over the last 10 matches.

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

This query calculates the cumulative runs scored by V. Kohli over his matches. It shows the match number, runs scored in each match, and the cumulative runs up to that match.

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

This query retrieves the top 5 bowlers for each team, ranked by the total number of wickets taken. The `DENSE_RANK` function is used to rank the bowlers within each team based on their total wickets.

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

This query calculates the cumulative runs scored by the specified batsmen (SA Yadav in this case) over their matches. It shows the match number, runs scored in each match, and the cumulative runs up to that match.

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

This query calculates V. Kohli's match-wise runs, cumulative runs, career average, and a rolling average over the last 10 matches. It shows the match number, runs scored in each match, cumulative runs, career average, and the rolling average.

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