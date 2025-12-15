## Entity Overview

**Aliases:** `officials`, `referees`, `game_referees`, `officiating_crew`

## Entity Summary
The `game_official` entity represents referee and official assignments for NBA games. Each record contains information about a specific official assigned to a specific game, including official identification and name information.

## Text-to-SQL Reasoning Guidelines

- **Record Structure:** Each record represents one official's assignment to one game. A game has multiple records (typically 3 officials per game).

- **Display Guidelines:** Use `first_name` and `last_name` together, or concatenate them for full name display. When grouping by official, group by `official_id`.

- **Official Analysis:** Group by `official_id` to analyze individual official assignments and game counts.

## Important Notes
- **Multiple Officials Per Game:** Each game typically has 3 officials, so each game will have 3 records in this entity.
- **Official Identification:** Use `official_id` to track individual officials across multiple games.

## Query Examples

**Example 1:** Officials for a specific game
```sql
SELECT first_name, last_name, official_name
FROM entity('game_official')
WHERE game_id = 12345
ORDER BY official_name;
```

**Example 2:** Most active officials
```sql
SELECT official_name, COUNT(*) as game_count
FROM entity('game_official')
GROUP BY official_name
ORDER BY game_count DESC
LIMIT 10;
```

**Example 3:** Games officiated by a specific official
```sql
SELECT game_id, official_name
FROM entity('game_official')
WHERE official_id = 12345
ORDER BY game_id DESC;
```
