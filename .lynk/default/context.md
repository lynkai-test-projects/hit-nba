# Default Domain Context

## Domain Purpose and Scope

**Purpose:** Central repository for core NBA data definitions and cross-domain entities

**Scope:** Foundation-level basketball data that supports all analytical use cases

**Data Governance:** Master data management for core NBA entities

## Important Notes
- **Player Names**: Use `full_name` field for display; names are also split into `first_name` and `last_name`
- **Team Names**: Use full team names (e.g., "Los Angeles Lakers"); teams may have historical names tracked in `franchise` entity
- **Game IDs**: Unique identifiers for each game
- **Season Years**: Always use ending year (2023 for 2022-23 season)
- **Time Fields**: Default time fields vary by entity (`game_date` for games, `start_date` for player-team affiliations)

## Temporal Scope
- **Start Year:** 1946 (NBA founding year)
- **End Year:** 2023 (last year of data coverage)
- **Historical Depth:** Complete statistical records since 1970s

### Season Filtering

Use `season_year` field for all season-based queries and filtering.

**Guidelines:**
- Always use `season_year` field when available (ending year of season, e.g., 2018 for 2017-18 season)
- If `season_year` unavailable, filter by `game_date`: NBA seasons run from October (year-1) to June (year)
- Example: 2018 season → `WHERE season_year = 2018` OR `WHERE game_date >= '2017-10-01' AND game_date <= '2018-06-30'`

## Query Best Practices

1. **Prefer combined entities** when querying multiple entity types
2. **Use season_year field** for season-based filtering when available
3. **Fall back to game_date filtering** only when season_year unavailable
4. **Consider entity priority** when multiple entities could answer the same question
5. **Leverage entity relationships** rather than manual joins when possible

## Data Quality Principles

1. **Single source of truth** for player and team identities
2. **Consistent naming conventions** across all domains
3. **Standardized date and time formats**
4. **Unified measurement units** (points, minutes, percentages)
5. **Historical continuity** through franchise moves and name changes
