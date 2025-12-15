## Entity Overview
**Aliases:** `coaching_assignments`, `coach_teams`, `staff_assignments`

## Entity Summary
The `coaching_staff` entity represents coaching staff assignments linking coaches to teams by season. Each record contains information about a specific coach's assignment to a specific team for a specific season, including role (head coach or assistant) and assignment details.

## Text-to-SQL Reasoning Guidelines

- **Record Structure:** Each record represents one coach's assignment to one team for one season. A coach can have multiple records (different teams or seasons).

- **Display Guidelines:** Use `coach_name` and `team_full_name` for display. Include `season` for temporal context.

- **Role Analysis:** Use `is_head_coach` to filter between head coaches and assistant coaches. Use `role` field for detailed role information.

- **Season Information:** Use `season_year` (number) for filtering and grouping. The `season` field contains string format (e.g., "2023-24").

- **Team Assignments:** Use `team_id` to join with `team` entity. Use `team_full_name` for display.

- **Coach Relationships:** Use `coach_id` to join with `coach` entity. Use `coach_name` for display.

### Role Information
- `role` (string) - Coach role (Head Coach, Assistant Coach, etc.)
- `is_head_coach` (boolean) - Whether coach is head coach

## Important Notes

- **Multiple Roles:** A coach can have multiple assignments in the same season (e.g., head coach for one team, assistant for another).
- **Role Filtering:** Use `is_head_coach = TRUE` to filter for head coaches only.
- **Season Format:** Use `season_year` (number) for filtering, `season` (string) for display.

## Query Examples

**Example 1:** Coaching staff for a specific team and season
```sql
SELECT coach_name, role, is_head_coach
FROM entity('coaching_staff')
WHERE team_full_name = 'Los Angeles Lakers' AND season_year = 2023
ORDER BY is_head_coach DESC, coach_name;
```

**Example 2:** Head coaches by season
```sql
SELECT season_year, team_full_name, coach_name
FROM entity('coaching_staff')
WHERE is_head_coach = TRUE
ORDER BY season_year DESC, team_full_name;
```

**Example 3:** Coach's career assignments
```sql
SELECT season_year, team_full_name, role
FROM entity('coaching_staff')
WHERE coach_id = 12345
ORDER BY season_year DESC;
```
