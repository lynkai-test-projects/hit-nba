## Entity Overview

**Aliases:** `coaches`, `basketball_coaches`, `nba_coaches`, `coach_dimension`, `dim_coach`, `coach_info`

## Entity Summary
The `coach` entity represents individual NBA basketball coaches with their personal information and career details. Each record contains information about a specific coach including unique coach identifiers, name information, and years of active coaching. This dimension table serves as the central reference point for all coach-related analysis.

## Text-to-SQL Reasoning Guidelines

- **Central Dimension:** The `coach` entity is the central dimension for connecting all coach assignments to teams and seasons. Always use `coach_id` for joins and grouping.

- **Primary Key:** The `coach_id` field is the primary key that links to `coaching_staff` assignment table.

- **Display Guidelines:** When displaying coach information, group by `coach_id` and `coach_name`, and display `coach_name` for readability.

- **Name Fields:** Coach names are split into `first_name`, `last_name`, and `coach_name` for flexible querying.

- **Career Span:** Use `first_year` and `last_year` to understand the coaching career span and filter coaches by active years.

- **Team Assignments:** Coach-team assignments are tracked in the `coaching_staff` entity. Use `coach_to_coaching_staff` join to access team assignments.

## Important Notes

- **Career Span:** Use `first_year` and `last_year` to filter coaches by active years. `last_year` is NULL for currently active coaches.

- **Team Assignments:** Coach-team relationships are tracked in `coaching_staff` entity, not directly in `coach` entity.

## Query Examples

**Example 1:** All coaches with their career spans

```sql
SELECT coach_name, first_year, last_year
FROM entity('coach')
ORDER BY first_year DESC;
```

**Example 2:** Currently active coaches

```sql
SELECT coach_name, first_year
FROM entity('coach')
WHERE last_year IS NULL
ORDER BY first_year DESC;
```

**Example 3:** Coaches by career length

```sql
SELECT coach_name, 
       first_year, 
       last_year,
       COALESCE(last_year, EXTRACT(YEAR FROM CURRENT_DATE())) - first_year as career_years
FROM entity('coach')
ORDER BY career_years DESC;
```