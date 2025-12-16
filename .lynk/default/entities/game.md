## Entity Overview

**Aliases:** `games`, `basketball_games`, `nba_games`, `game_dimension`, `dim_game`, `game_info`

## Entity Summary
The `game` entity represents individual NBA basketball games with their detailed metadata, scheduling information, season classification, and game context. This dimension table serves as the central reference point for all game-related analysis, connecting teams, players, statistics, and events to specific games.

## Text-to-SQL Guidelines

When generating SQL code, you myst take into account the following guidelines:

- **Season Type Filtering:** Unless specified explicitly, always add the filter `season_type NOT IN ('Pre Season', 'All-Star')`.

- **Time-Based Analysis:** Use `game_date` to filter games within specific date ranges or seasons. Use `season_year` for season-level analysis.

- **Display Guidelines:** For visualizations, use `season_year`. Use `game_date` for date-based queries.

## Key Fields

### Game Status (from `game_summary` asset)
- `game_status_text` (string) - Game status (Final, Live, Scheduled)
- `is_live_game` (boolean) - Whether game is currently live
- `is_final_game` (boolean) - Whether game is final/completed
- `is_scheduled_game` (boolean) - Whether game is scheduled but not started
- `is_nationally_televised` (boolean) - Whether game is nationally televised
- `natl_tv_broadcaster` (string) - National TV broadcaster abbreviation

### Teams
- `home_team_id` (number) - Home team identifier
- `visitor_team_id` (number) - Visitor team identifier

## Important Notes

- **Season Type:** Always consider `season_type` when analyzing games. Filter out Pre Season and All-Star games unless specifically requested.
- **Game Status:** Use status fields to identify game state (live, final, scheduled).
- **Date Filtering:** Use `game_date` for temporal queries, `season_year` for season-level analysis.

## Query Examples

**Example 1:** Games by season type
```sql
SELECT season_type, COUNT(*) as game_count
FROM entity('game')
WHERE season_type IN ('Regular Season', 'Playoffs')
GROUP BY season_type;
```

**Example 2:** Live games
```sql
SELECT game_id, game_date, home_team_id, visitor_team_id, game_status_text
FROM entity('game')
WHERE is_live_game = TRUE;
```

**Example 3:** Nationally televised games
```sql
SELECT game_id, game_date, season_type, natl_tv_broadcaster
FROM entity('game')
WHERE is_nationally_televised = TRUE
ORDER BY game_date DESC;
```
