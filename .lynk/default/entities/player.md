## Entity Overview

**Aliases:** `players`, `basketball_players`, `nba_players`, `player_dimension`, `dim_player`, `player_info`

## Entity Summary
The `player` entity represents individual NBA basketball players with their personal information, career status, and demographic details. This dimension table serves as the central reference point for all player-related analysis, connecting player statistics, performance data, and career information across seasons and teams.

## Text-to-SQL Reasoning Guidelines

- **Central Dimension:** The `player` entity is the central dimension for connecting all player statistics and performance data. Always use `id` for joins and grouping.

- **Display Guidelines:** When displaying player information, group by `id` and `full_name`, and display `full_name` for readability.

- **Active Status:** Use `is_active` to filter between currently active (1) and inactive (0) players. Filter by `is_active = 1` when users ask about "current players" or "active players".

- **Name Fields:** Use `full_name` for display and search. Names are also available as `first_name` and `last_name` for flexible querying.

- **Position Analysis:** Use `position` (standardized: G, F, C, F-G, C-F) or `position_raw` for positional analysis.

- **Experience Levels:** Use `experience` (years) or `experience_bracket` for career stage analysis. `is_rookie` identifies players with 0 years experience.

- **Physical Attributes:** Use `height_inches` (converted to total inches) or `height` (feet-inches format), `weight_pounds`, and `country` for demographic analysis.

- **Draft Information:** Draft fields (`draft_position`, `draft_year`, `draft_round`, `drafting_team`, `pre_nba_organization`) come from `draft` entity via `first_last` features (represents first draft only).

- **Career Statistics:** Career stats (points, rebounds, assists, etc.) come from `player_game` entity via metric features. These aggregate all game-level statistics.

- **Team Affiliations:** Team metrics (`total_team_affiliations`, `avg_team_tenure_years`, `career_team_changes`) come from `player_team` entity.

- **Dunk Statistics:** Dunk metrics (`career_dunks`, `avg_dunks`) come from `player_game` entity.

- **Inactive Player Metrics:** Inactive metrics (`career_inactive_games`, `career_active_games`, `inactive_game_percentage`) come from `player_game` entity.

- **Coach Relationships:** Coach metrics (`total_head_coaches_played_under`, `coaching_stints_count`) come from `coaching_staff` entity.

## Key Fields

### Status and Demographics
- `is_active` (boolean) - Whether player is currently active (1) or inactive (0)
- `position` (string) - Standardized position (G, F, C, F-G, C-F)
- `experience` (number) - Years of NBA experience
- `is_rookie` (boolean) - Whether player is a rookie (0 years experience)
- `country` (string) - Player's country of origin

## Important Notes

- **Primary Key:** Always use `id` for joins and grouping to ensure accurate results.
- **Active Filtering:** Use `is_active = 1` for current players, `is_active = 0` for former players.
- **Draft Data:** Draft fields represent first draft only (via `first_last` features from `draft` entity).
- **Career Stats:** Career statistics aggregate all game-level data from `player_game` entity.
- **Null Handling:** Some players may not have draft information (undrafted players).

## Query Examples

**Example 1:** Active players by position
```sql
SELECT position, COUNT(*) as player_count
FROM entity('player')
WHERE is_active = 1
GROUP BY position
ORDER BY player_count DESC;
```

**Example 2:** Top players by career points
```sql
SELECT full_name, career_points, career_games, career_assists
FROM entity('player')
ORDER BY career_points DESC
LIMIT 10;
```

**Example 3:** Players drafted in top 5
```sql
SELECT full_name, draft_year, draft_position, position
FROM entity('player')
WHERE draft_position <= 5
ORDER BY draft_year DESC, draft_position ASC;
```
