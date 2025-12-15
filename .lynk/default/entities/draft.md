## Entity Overview

**Aliases:** `drafts`, `draft_combine_stats`, `draft_stats`, `combine_stats`, `player_physical_stats`, `player_seasons`

## Entity Summary
The `draft` entity represents NBA Draft Combine statistics and draft history data. Each record contains a player's combine measurements, athletic testing results, and draft information for a specific season. This combines physical measurements (height, weight, wingspan) with athletic performance (vertical leap, agility, speed) and draft outcomes (position, year, round, team).

## Text-to-SQL Reasoning Guidelines

- **Draft Combine Data:** Contains physical measurements and athletic testing from NBA Draft Combine. Use `height_wo_shoes` for standardized height comparisons. Measurements are in inches (weight in pounds).

- **Draft History:** Draft information (`draft_position`, `draft_year`, `draft_round`, `drafting_team_id`) comes from `draft_history` asset via `draft_to_draft_history` join. May be NULL for undrafted players.

- **Display Guidelines:** Use `player_name` for display and include `season` for temporal context. Group by `player_id`, `player_name`, and `season` for player combine history.

- **Draft Position:** Lower numbers = higher picks (pick 1 is first overall). Use `is_first_round` and `is_lottery_pick` for categorical analysis.

- **Athletic Testing:** Lower times = better for speed/agility tests. Higher values = better for strength/leap tests. Some fields may be NULL if tests weren't completed.

- **Pre-NBA Organization:** Use `pre_nba_organization` to analyze backgrounds (College/University, High School, Other Team/Club). `international_players` metric identifies non-college players.

- **Filtering:** Filter by `season` for combine participation by year. Filter by `draft_position IS NOT NULL` to focus on drafted players only.

## Key Fields

### Draft History (from `draft_history` asset)
- `draft_position` (number) - Overall draft pick position (NULL if undrafted)
- `draft_year` (number) - Year drafted (NULL if undrafted)
- `draft_round` (number) - Draft round (NULL if undrafted)
- `drafting_team_id` (number) - Team that drafted player (NULL if undrafted)
- `pre_nba_organization` (string) - Pre-NBA background (College/University, High School, Other Team/Club)

### Calculated Fields
- `is_first_round` (boolean) - Whether pick was in first round
- `is_lottery_pick` (boolean) - Whether pick was lottery (picks 1-14)

## Important Notes
- **Multiple Records:** A player can have multiple records if they participated in combine in multiple seasons.
- **NULL Draft Data:** Draft history fields may be NULL for players who participated in combine but were not drafted.
- **Measurement Units:** All measurements in inches (weight in pounds). Use `height_wo_shoes` for standardized comparisons.
- **Shooting Data:** Shooting percentages stored as strings. Some players may not have shooting data.

## Query Examples

**Example 1:** Combine statistics for a specific season
```sql
SELECT player_name, season, height_wo_shoes, wingspan, max_vertical_leap
FROM entity('draft')
WHERE season = 2023
ORDER BY player_name;
```

**Example 2:** Draft position analysis
```sql
SELECT player_name, draft_year, draft_position, height_wo_shoes, max_vertical_leap
FROM entity('draft')
WHERE is_lottery_pick = TRUE
ORDER BY draft_position ASC;
```

**Example 3:** Average physical attributes by season
```sql
SELECT season, 
       metric(avg_height_no_shoes) as avg_height,
       metric(avg_wingspan) as avg_wingspan,
       metric(count_drafts) as player_count
FROM entity('draft')
GROUP BY season
ORDER BY season DESC;
```
