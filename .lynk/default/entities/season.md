## Entity Overview

**Aliases:** `seasons`, `nba_seasons`, `season_dimension`, `dim_season`, `season_info`, `championships`, `finals_results`

## Entity Summary
The `season` entity represents individual NBA seasons with their championship outcomes, team performance data, and key season metrics. Each record contains information about a specific season including the season year, championship team, defeated team in finals, wins for both teams, and calculated performance metrics.

## Text-to-SQL Reasoning Guidelines

- **MVP Fields:** The dataset contains TWO MVP fields: `regular_season_mvp` (regular season MVP winner's full name) and `finals_mvp` (Finals MVP winner's full name). When users ask about "MVP awards", "most MVPs", or "MVP winners" without specifying, they typically mean regular season MVP. When users ask about "Finals MVP" or "championship MVP", use `finals_mvp`. Always clarify which MVP type is being asked about, or use the appropriate field based on context.

- **Display Guidelines:** Use `season_display` for human-readable format (e.g., "2023-24") or `season_year` for numeric filtering.

- **Championship Data:** The `champion_team` and `defeated_team` fields contain the full team names of finals participants. Use `champ_wins` and `def_wins` for games won by each team.

- **Series Competitiveness:** Use `is_sweep`, `is_seven_game_series`, and `is_close_series` to analyze series competitiveness. These boolean fields help identify dominant vs competitive championships.

- **MVP Information:** The dataset contains two MVP fields: `regular_season_mvp` contains the full name of the NBA Most Valuable Player (regular season MVP) for the season, and `finals_mvp` contains the full name of the NBA Finals Most Valuable Player (Finals MVP) for the championship series. When users ask about "MVP awards", "most MVPs", or "MVP winners" without specifying, they typically mean regular season MVP. When users ask about "Finals MVP" or "championship MVP", use `finals_mvp`.

- **MVP to Player Joining:** To aggregate MVP awards by player or connect MVP data to player records, use the defined relationships: `season_regular_mvp_to_player` (for regular season MVP) or `season_finals_mvp_to_player` (for Finals MVP). These relationships automatically match `regular_season_mvp` or `finals_mvp` (from season) with `full_name` (from player). You can also use direct SQL joins: `JOIN entity('player') p ON s.regular_season_mvp = p.full_name` for regular season MVP, or `JOIN entity('player') p ON s.finals_mvp = p.full_name` for Finals MVP. This allows counting total MVPs per player, analyzing MVP winners' career stats, and answering questions like "which player won the most MVP awards?" by grouping MVP winners and counting occurrences.

- **Historical Analysis:** Use `season_year` for temporal filtering and historical trend analysis. The entity spans all NBA seasons in the dataset.

### MVP Information
- `regular_season_mvp` (string) - Full name of NBA Most Valuable Player (regular season MVP). This field contains the player's full name and should be matched to `player.full_name` to join MVP data to player records. Use this field when users ask about "MVP awards", "most MVPs", or "MVP winners" without specifying Finals MVP.
- `finals_mvp` (string) - Full name of NBA Finals Most Valuable Player (Finals MVP). This field contains the player's full name and should be matched to `player.full_name` to join Finals MVP data to player records. Use this field when users ask about "Finals MVP", "championship MVP", or explicitly mention Finals.

## Important Notes

- **Season Year:** Represents the ending year of the season (e.g., 2023 for 2023-24 season).
- **Championship Data:** Always includes both champion and defeated team information.
- **Series Length:** Calculated as `champ_wins + def_wins` (typically 4-7 games).
- **MVP Data:** The dataset contains two MVP fields: `regular_season_mvp` (regular season MVP winner's full name) and `finals_mvp` (Finals MVP winner's full name). When users ask about "MVP awards", "most MVPs", or "MVP winners" without specifying, they typically mean regular season MVP. When users ask about "Finals MVP" or "championship MVP", use `finals_mvp`. MVP information may not be available for all seasons. 

- **Using MVP Relationships:** When aggregating MVP awards by player (e.g., "which player won the most regular season MVP awards?"), use the `season_regular_mvp_to_player` relationship by joining `season` to `player` entity: `JOIN entity('player') p ON s.regular_season_mvp = p.full_name`. This relationship automatically matches MVP names to player records. Always group by `p.id` and `p.full_name` to ensure accurate player-level aggregation. For Finals MVP questions, use `season_finals_mvp_to_player` relationship with `finals_mvp` field.

## Query Examples

**Example 1:** Championship results by season
```sql
SELECT season_display, champion_team, defeated_team, champ_wins, def_wins
FROM entity('season')
ORDER BY season_year DESC;
```

**Example 2:** Most competitive finals series
```sql
SELECT season_display, champion_team, defeated_team, series_length
FROM entity('season')
WHERE is_seven_game_series = TRUE
ORDER BY season_year DESC;
```

**Example 3:** Regular season MVP information by season
```sql
SELECT season_display, regular_season_mvp, champion_team
FROM entity('season')
WHERE regular_season_mvp IS NOT NULL
ORDER BY season_year DESC;
```

**Example 4:** Finals MVP information by season
```sql
SELECT season_display, finals_mvp, champion_team, defeated_team
FROM entity('season')
WHERE finals_mvp IS NOT NULL
ORDER BY season_year DESC;
```

**Example 5:** Count regular season MVP awards by player (most MVPs) - Use relationship `season_regular_mvp_to_player`
```sql
SELECT 
  p.full_name as player_name,
  COUNT(*) as mvp_count,
  STRING_AGG(s.season_display, ', ' ORDER BY s.season_year DESC) as mvp_seasons
FROM entity('season') s
JOIN entity('player') p ON s.regular_season_mvp = p.full_name
WHERE s.regular_season_mvp IS NOT NULL
GROUP BY p.id, p.full_name
ORDER BY mvp_count DESC, player_name;
```
**Note:** This query uses the `season_regular_mvp_to_player` relationship implicitly through the JOIN condition. When users ask "which player won the most regular season MVP awards?" or "give me the player that won the most season MVP awards", use this pattern with `regular_season_mvp` field.

**Example 6:** Count Finals MVP awards by player (most Finals MVPs) - Use relationship `season_finals_mvp_to_player`
```sql
SELECT 
  p.full_name as player_name,
  COUNT(*) as finals_mvp_count,
  STRING_AGG(s.season_display, ', ' ORDER BY s.season_year DESC) as finals_mvp_seasons
FROM entity('season') s
JOIN entity('player') p ON s.finals_mvp = p.full_name
WHERE s.finals_mvp IS NOT NULL
GROUP BY p.id, p.full_name
ORDER BY finals_mvp_count DESC, player_name;
```
**Note:** This query uses the `season_finals_mvp_to_player` relationship implicitly through the JOIN condition. When users ask "which player won the most Finals MVP awards?", use this pattern with `finals_mvp` field.

**Example 7:** Regular season MVP winners with their career statistics - Use relationship `season_regular_mvp_to_player`
```sql
SELECT 
  p.full_name as player_name,
  COUNT(*) as mvp_count,
  p.career_points,
  p.career_games,
  p.position
FROM entity('season') s
JOIN entity('player') p ON s.regular_season_mvp = p.full_name
WHERE s.regular_season_mvp IS NOT NULL
GROUP BY p.id, p.full_name, p.career_points, p.career_games, p.position
ORDER BY mvp_count DESC;
```

