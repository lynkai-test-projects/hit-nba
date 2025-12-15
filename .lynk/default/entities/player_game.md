## Entity Overview

**Aliases:** `player_game_stats`, `player_games`, `box_scores`, `player_performance`, `game_stats`, `player_game_logs`

## Entity Summary
The `player_game` entity represents individual NBA player performance in specific games with comprehensive box score statistics. Each record contains detailed performance metrics for a single player in a single game, including scoring, assists, rebounds, shooting percentages, and other statistical metrics.

## Text-to-SQL Reasoning Guidelines

- **Inactive Player Filtering (CRITICAL):** Unless explicitly asked about inactive players, ALWAYS filter out inactive player games by adding `WHERE is_inactive = FALSE` (or `AND is_inactive = FALSE` if other conditions exist). The `is_inactive` field indicates players who were on the inactive list and did not play in the game. Inactive player records should be excluded from all performance analysis, statistics, and queries unless the user specifically asks about inactive players, inactive games, or inactive patterns. This is the default behavior for all queries involving player_game entity.

- **Record Structure:** Each record represents one player's performance in one game (player + game combination). A player can have multiple records (different games), and each game has multiple records (different players).

- **Display Guidelines:** Use `first_name` and `last_name` together, or concatenate them for full name display. When grouping by player, always group by `person_id` to ensure accurate aggregation.

- **Game Type Filtering:** Use `game_type` to filter between Regular Season, Playoffs, All-Star, and Pre Season games. Performance metrics can vary significantly by game type.

- **Home vs Away:** Use the `home` boolean field to analyze home vs away performance differences. `home = TRUE` means player's team was home.

- **Win/Loss Context:** Use `win` boolean field to analyze performance in wins vs losses. `win = TRUE` means player's team won.

- **Null Handling:** Field goal percentages, three-point percentages, and free throw percentages may be NULL for games with zero attempts. Use `COALESCE` or handle NULL values appropriately when aggregating.

- **Shooting Percentages:** Calculate as `(made / attempted) * 100`. Handle division by zero cases.

- **Time Fields:** Use `game_date` for temporal filtering and date range queries.

## Key Fields

### Game Context
- `game_date` (datetime) - Date of the game
- `game_type` (string) - Type of game (Regular Season, Playoffs, All-Star, Pre Season)
- `win` (boolean) - Whether player's team won
- `home` (boolean) - Whether player's team was home
- `is_inactive` (boolean) - Whether player was inactive for the game. **IMPORTANT:** Filter by `is_inactive = FALSE` by default unless explicitly asked about inactive players.

## Important Notes
- **Inactive Players (DEFAULT BEHAVIOR):** ALWAYS filter by `is_inactive = FALSE` to exclude inactive players from performance analysis UNLESS the user explicitly asks about inactive players, inactive games, inactive patterns, or inactive statistics. This is the default behavior for all queries. Only include inactive player games when the user specifically requests information about inactive players.

- **Null Percentages:** Shooting percentages may be NULL for games with zero attempts. Handle appropriately in aggregations.

- **Game Type:** Always consider `game_type` when analyzing performance, as metrics vary significantly between Regular Season and Playoffs.

## Query Examples

**Example 1:** Player game statistics for a specific game
```sql
SELECT first_name, last_name, points, assists, rebounds_total, num_minutes
FROM entity('player_game')
WHERE game_id = '12345' AND is_inactive = FALSE
ORDER BY points DESC;
```

**Example 2:** Player's season averages
```sql
SELECT first_name, last_name,
       AVG(points) as avg_points,
       AVG(assists) as avg_assists,
       AVG(rebounds_total) as avg_rebounds
FROM entity('player_game')
WHERE person_id = '12345' AND game_type = 'Regular Season' AND is_inactive = FALSE
GROUP BY first_name, last_name;
```

**Example 3:** Top scoring games
```sql
SELECT first_name, last_name, game_date, points, player_team_name, opponent_team_name
FROM entity('player_game')
WHERE is_inactive = FALSE
ORDER BY points DESC
LIMIT 10;
```
