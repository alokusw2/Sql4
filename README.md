# Sql4

1 Problem 1 : The Number of Seniors and Juniors to Join the Company	(https://leetcode.com/problems/the-number-of-seniors-and-juniors-to-join-the-company/)

A company wants to hire new employees. The budget of the company for the salaries is $70000. The company's criteria for hiring are:

Hiring the largest number of seniors.
After hiring the maximum number of seniors, use the remaining budget to hire the largest number of juniors.
Write a solution to find the number of seniors and juniors hired under the mentioned criteria.

Return the result table in any order.

---------------------------------------------------------------------------------------------------------


WITH cte AS (
  SELECT *,
         SUM(salary) OVER (PARTITION BY experience ORDER BY salary) AS cum
  FROM Candidates
),
senior AS (
  SELECT COUNT(*) cnt
  FROM cte
  WHERE experience = 'Senior' AND cum <= 70000
),
junior AS (
  SELECT COUNT(*) cnt
  FROM cte
  WHERE experience = 'Junior'
    AND cum <= 70000 - COALESCE((SELECT MAX(cum) FROM cte WHERE experience = 'Senior' AND cum <= 70000), 0)
)
SELECT 'Senior' AS experience, cnt AS accepted_candidates FROM senior
UNION ALL
SELECT 'Junior', cnt FROM junior;



---------------------------------------------------------------------------------------------------------

2 Problem 2 : League Statistics		(https://leetcode.com/problems/league-statistics/ )

---------------------------------------------------------------------------------------------------------
Write a solution to report the statistics of the league. The statistics should be built using the played matches where the winning team gets three points and the losing team gets no points. If a match ends with a draw, both teams get one point.

Each row of the result table should contain:

team_name - The name of the team in the Teams table.
matches_played - The number of matches played as either a home or away team.
points - The total points the team has so far.
goal_for - The total number of goals scored by the team across all matches.
goal_against - The total number of goals scored by opponent teams against this team across all matches.
goal_diff - The result of goal_for - goal_against.
Return the result table ordered by points in descending order. If two or more teams have the same points, order them by goal_diff in descending order. If there is still a tie, order them by team_name in lexicographical order.
---------------------------------------------------------------------------------------------------------
 SELECT team_name,
       COUNT(*) matches_played,
       SUM(pts) points,
       SUM(gf) goal_for,
       SUM(ga) goal_against,
       SUM(gf - ga) goal_diff
FROM (
  SELECT home_team_id id, home_team_goals gf, away_team_goals ga,
         IF(home_team_goals > away_team_goals, 3, IF(home_team_goals = away_team_goals, 1, 0)) pts
  FROM Matches
  UNION ALL
  SELECT away_team_id, away_team_goals, home_team_goals,
         IF(away_team_goals > home_team_goals, 3, IF(away_team_goals = home_team_goals, 1, 0))
  FROM Matches
) m JOIN Teams t ON m.id = t.team_id
GROUP BY team_name
ORDER BY points DESC, goal_diff DESC, team_name;






---------------------------------------------------------------------------------------------------------


3 Problem 3 : Sales Person		(https://leetcode.com/problems/sales-person/ )

Write a solution to find the names of all the salespersons who did not have any orders related to the company with the name "RED".

Return the result table in any order.

The result format is in the following example.

 
---------------------------------------------------------------------------------------------------------
SELECT name
FROM SalesPerson
WHERE sales_id NOT IN (
  SELECT sales_id FROM Orders o JOIN Company c ON o.com_id = c.com_id WHERE c.name = 'RED'
);





---------------------------------------------------------------------------------------------------------

4 Problem 4 : Friend Requests II	(https://leetcode.com/problems/friend-requests-ii-who-has-the-most-friends/ )


---------------------------------------------------------------------------------------------------------
Write a solution to find the people who have the most friends and the most friends number.

The test cases are generated so that only one person has the most friends.
---------------------------------------------------------------------------------------------------------
WITH A AS (
  SELECT requester_id AS id FROM RequestAccepted
  UNION ALL
  SELECT accepter_id FROM RequestAccepted
)
SELECT id, COUNT(*) AS num
FROM A
GROUP BY id
ORDER BY num DESC
LIMIT 1;




---------------------------------------------------------------------------------------------------------
