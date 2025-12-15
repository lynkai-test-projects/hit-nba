## Entity Overview

**Aliases:** `player_team_stats`, `player_affiliations`, `team_affiliations`, `player_tenure`

## Entity Summary
The `player_team` entity represents player-team affiliation periods throughout a player's career. Each record contains information about a specific player's tenure with a specific team, including start and end dates, season information, and tenure duration.

## Text-to-SQL Reasoning Guidelines

- **Record Structure:** Each record represents one player's affiliation with one team for a specific period. A player can have multiple records (different teams or different periods with the same team).

- **Display Guidelines:** Use `player_full_name` and `team_full_name` for display. When grouping by player, group by `person_id`.

- **Tenure Analysis:** Use `tenure_years` to analyze how long players stayed with teams. Use `start_date` and `end_date` for temporal filtering.

- **Season Information:** Use `season_year` for season-level analysis. The `season` field contains string format (e.g., "2023-24").

- **Current Affiliations:** Use `end_date IS NULL` to identify current team affiliations.

- **Team Changes:** Count distinct `team_full_name` values grouped by `person_id` to find how many teams a player has played for.

## Key Fields

### Temporal Information
- `start_date` (datetime) - Start date of affiliation
- `end_date` (datetime) - End date of affiliation (NULL if current)
- `season` (string) - NBA season (e.g., "2023-24")
- `season_year` (number) - Season year (ending year)

### Tenure Information
- `tenure_years` (formula) - Number of years with team
- `is_current` (formula) - Whether affiliation is current

## Important Notes

- **Multiple Records:** A player can have multiple records for different teams or different periods.
- **Current Affiliations:** Use `end_date IS NULL` or `is_current = TRUE` to identify current team affiliations.
- **Tenure Calculation:** `tenure_years` is calculated from `start_date` and `end_date`.

## Query Examples

**Example 1:** Player's team history
```sql
SELECT player_full_name, team_full_name, start_date, end_date, tenure_years
FROM entity('player_team')
WHERE person_id = '12345'
ORDER BY start_date DESC;
```

**Example 2:** Current team affiliations
```sql
SELECT player_full_name, team_full_name, start_date
FROM entity('player_team')
WHERE is_current = TRUE
ORDER BY player_full_name;
```

**Example 3:** Players with most team changes
```sql
SELECT player_full_name, COUNT(DISTINCT team_full_name) as team_count
FROM entity('player_team')
GROUP BY player_full_name
ORDER BY team_count DESC
LIMIT 10;
```
