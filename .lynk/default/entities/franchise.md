## Entity Overview

**Aliases:** `franchises`, `team_history`, `historical_teams`, `team_iterations`

## Entity Summary
The `franchise` entity represents historical iterations of NBA franchises. Each record represents a specific franchise identity defined by its team_id, city, nickname, and active period. This enables analysis of team relocations, name changes, and franchise evolution.

## Text-to-SQL Reasoning Guidelines

- **Current vs Historical:** Use `is_current` to identify currently active franchise iterations. Use `is_historical` for past iterations. `year_active_till` is NULL for current franchises.

- **Display Guidelines:** Use `franchise_name` (city + nickname) for display. Include `year_founded` and `year_active_till` for temporal context.

- **Franchise Evolution:** Group by `team_id` and order by `year_founded` to trace the full history of a franchise, including relocations and name changes.

- **Longevity Analysis:** Use `years_active` to determine how long a franchise iteration existed.

## Important Notes
- **Multiple Iterations:** A single `team_id` can have multiple records representing different historical identities (relocations, name changes).
- **Current Identification:** `year_active_till IS NULL` indicates currently active franchise iterations.
- **Temporal Validity:** `year_founded` and `year_active_till` define the lifespan of each iteration.

## Query Examples

**Example 1:** Historical franchise iterations
```sql
SELECT franchise_name, year_founded, year_active_till, years_active
FROM entity('franchise')
WHERE is_historical = TRUE
ORDER BY year_founded ASC;
```

**Example 2:** Franchise history for a specific team
```sql
SELECT franchise_name, year_founded, year_active_till, years_active
FROM entity('franchise')
WHERE team_id = 1610612747
ORDER BY year_founded ASC;
```

**Example 3:** Current vs historical franchises
```sql
SELECT CASE WHEN is_current THEN 'Current' ELSE 'Historical' END as status,
       COUNT(*) as count
FROM entity('franchise')
GROUP BY status;
```
