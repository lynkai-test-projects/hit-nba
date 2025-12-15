# HIT-NBA Data Project

## 📊 Data Cleaning and Public Table Creation

This section details the SQL queries used to clean and transform the raw data from the `nba.raw_data` schema into a more refined and usable format within the `nba.public` schema. These transformations address data inconsistencies (like standardizing 'All Star' to 'All-Star') and deduplicate records based on identified primary keys.

The `CREATE OR REPLACE TABLE` statements ensure that these public tables are always up-to-date when the script is run.

```sql
# nba-demo
# SQL queries for cleaning and creating public tables in Snowflake

-- Create or replace the 'game' table with cleaned 'season_type' and deduplicated rows
CREATE OR REPLACE TABLE nba.public.game AS
WITH deduped AS (
    SELECT
        * EXCLUDE (season_type), -- Exclude the original season_type
        ROW_NUMBER() OVER (PARTITION BY game_id ORDER BY game_date) AS rn,
        CASE
            WHEN season_type = 'All Star' THEN 'All-Star' -- Standardize 'All Star' to 'All-Star'
            ELSE season_type
        END AS season_type_cleaned -- New column with cleaned season type
    FROM nba.raw_data.game
)
SELECT
    d.* EXCLUDE (rn), -- Select all columns from deduped CTE, excluding the row number
    season_type_cleaned AS season_type -- Use the cleaned season_type as the final season_type
FROM deduped d
WHERE rn = 1; -- Keep only one row per game_id (the first one based on game_date)

-- Create or replace 'game_info' table, deduplicating by game_id
CREATE OR REPLACE TABLE nba.public.game_info AS
    SELECT *
    FROM nba.raw_data.game_info
    QUALIFY ROW_NUMBER() OVER(PARTITION BY game_id ORDER BY game_id) = 1;

-- Create or replace 'game_summary' table, deduplicating by game_id
CREATE OR REPLACE TABLE nba.public.game_summary AS
    SELECT *
    FROM nba.raw_data.game_summary
    QUALIFY ROW_NUMBER() OVER(PARTITION BY game_id ORDER BY game_id) = 1;

-- Create or replace 'inactive_players' table, deduplicating by player_id and game_id
CREATE OR REPLACE TABLE nba.public.inactive_players AS
    SELECT *
    FROM nba.raw_data.inactive_players
    QUALIFY ROW_NUMBER() OVER(PARTITION BY player_id, game_id ORDER BY game_id) = 1;

-- Create or replace 'line_score' table, deduplicating by game_id and game_sequence
CREATE OR REPLACE TABLE nba.public.line_score AS
    SELECT *
    FROM nba.raw_data.line_score
    QUALIFY ROW_NUMBER() OVER(PARTITION BY game_id, game_sequence ORDER BY game_id) = 1;

-- Create or replace 'officials' table, deduplicating by game_id and official_id
CREATE OR REPLACE TABLE nba.public.officials AS
    SELECT *
    FROM nba.raw_data.officials
    QUALIFY ROW_NUMBER() OVER(PARTITION BY game_id, official_id ORDER BY game_id) = 1;

-- Create or replace 'other_stats' table, deduplicating by game_id
CREATE OR REPLACE TABLE nba.public.other_stats AS
    SELECT *
    FROM nba.raw_data.other_stats
    QUALIFY ROW_NUMBER() OVER(PARTITION BY game_id ORDER BY game_id) = 1;

-- Create or replace 'play_by_play' table, deduplicating by game_id and eventnum
CREATE OR REPLACE TABLE nba.public.play_by_play AS
    SELECT *
    FROM nba.raw_data.play_by_play
    QUALIFY ROW_NUMBER() OVER(PARTITION BY game_id, eventnum ORDER BY game_id) = 1;