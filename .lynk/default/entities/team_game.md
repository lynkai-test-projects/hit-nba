## Entity Overview

**Aliases:** `team_game_stats`, `team_games`, `team_box_scores`, `team_performance`

## Entity Summary
The `team_game` entity represents team-level performance statistics for each NBA game. Each record contains aggregated team statistics for a single team in a single game, including scoring, rebounds, assists, shooting percentages, and other team-level metrics.

## Text-to-SQL Reasoning Guidelines

- **Record Structure:** Each record represents one team's performance in one game. Each game has two records (one for each team - home and away).

- **Display Guidelines:** Use `team_full_name` for display. When grouping by team, always group by `team_full_name` to ensure accurate aggregation.

- **Game Type Filtering:** Use `game_type` to filter between Regular Season, Playoffs, All-Star, and Pre Season games.

- **Home vs Away:** Use the `home` boolean field to analyze home vs away performance. `home = TRUE` means team was home.

- **Win/Loss:** Use `win` boolean field to analyze performance in wins vs losses. `win = TRUE` means team won.

- **Opponent Information:** Each record includes `opponent_team_name` to identify the opposing team.

- **Null Handling:** Shooting percentages may be NULL for games with zero attempts. Handle appropriately in aggregations.

## Important Notes

- **Two Records Per Game:** Each game has two records (one for each team). Use `game_id` to link records from the same game.
- **Team Name:** Use `team_full_name` for grouping and joining. This matches the `full_name` field in the `team` entity.
- **Game Type:** Always consider `game_type` when analyzing performance, as metrics vary between Regular Season and Playoffs.

## Query Examples

**Example 1:** Team game statistics for a specific game
```sql
SELECT team_full_name, points, rebounds_total, assists, win, home
FROM entity('team_game')
WHERE game_id = '12345'
ORDER BY home DESC;
```

**Example 2:** Team's season averages
```sql
SELECT team_full_name,
       AVG(points) as avg_points,
       AVG(rebounds_total) as avg_rebounds,
       AVG(assists) as avg_assists,
       SUM(CASE WHEN win THEN 1 ELSE 0 END) as wins
FROM entity('team_game')
WHERE team_full_name = 'Los Angeles Lakers' AND game_type = 'Regular Season'
GROUP BY team_full_name;
```

**Example 3:** Highest scoring team games
```sql
SELECT team_full_name, game_date, points, opponent_team_name, win
FROM entity('team_game')
WHERE game_type = 'Regular Season'
ORDER BY points DESC
LIMIT 10;
```
