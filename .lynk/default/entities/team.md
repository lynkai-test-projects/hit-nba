## Entity Overview

**Aliases:** `teams`, `basketball_teams`, `nba_teams`, `team_dimension`, `dim_team`, `team_info`, `franchises`

## Entity Summary
The `team` entity represents individual NBA basketball teams with their organizational information, geographic location, and franchise details. This dimension table serves as the central reference point for all team-related analysis, connecting team statistics, performance data, and organizational information across seasons.

## Text-to-SQL Reasoning Guidelines

- **Central Dimension:** The `team` entity is the central dimension for connecting all team statistics and performance data. Always use `id` for joins and grouping.

- **Display Guidelines:** Use `full_name` for display purposes. Team names are split into `city`, `nickname`, and `full_name` for flexible querying.

- **Geographic Analysis:** Use `city` and `state` fields for geographic analysis and location-based queries.

- **Franchise History:** Use `year_founded` to analyze team history and franchise longevity. Teams can have historical iterations tracked in the `franchise` entity.

- **Team Details:** Additional team information (arena, owner, coach, social media) comes from `team_details` asset. Use `join_name: team_id` to access these fields.

- **Draft History:** Draft-related metrics come from the `draft` entity via `team_to_draft` join (using `drafting_team_id`).

## Key Fields

### Team Details (from `team_details` asset)
- `arena` (string) - Name of team's home arena
- `arena_capacity` (number) - Seating capacity of arena
- `owner` (string) - Name of team's owner
- `head_coach_name` (string) - Name of team's head coach
- `general_manager` (string) - Name of team's general manager

## Important Notes

- **Team Name:** Use `full_name` for grouping and joining. This matches `team_full_name` in fact tables.
- **Franchise History:** Teams can have multiple historical iterations tracked in the `franchise` entity.
- **Team Details:** Some fields may be NULL if team details are not available.

## Query Examples

**Example 1:** All teams with their locations
```sql
SELECT full_name, city, state, year_founded
FROM entity('team')
ORDER BY full_name;
```

**Example 2:** Teams by arena capacity
```sql
SELECT full_name, arena, arena_capacity, arena_size_category
FROM entity('team')
WHERE arena_capacity IS NOT NULL
ORDER BY arena_capacity DESC;
```

**Example 3:** Teams with social media presence
```sql
SELECT full_name, has_social_media, social_media_count
FROM entity('team')
WHERE has_social_media = TRUE
ORDER BY social_media_count DESC;
```
