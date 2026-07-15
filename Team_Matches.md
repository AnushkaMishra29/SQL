#### Q1.  You are given a list of football teams participating in a round-robin tournament. Write a query to generate every unique  match pairing such that each team plays every other team exactly once.

SELECT
    a.team_id   AS team_1_id,
    a.team_name AS team_1_name,
    b.team_id   AS team_2_id,
    b.team_name AS team_2_name
FROM teams AS a
JOIN teams AS b
##### ON a.team_id < b.team_id
ORDER BY
    a.team_id,
    b.team_id;

#### Q2. Each team must now play every other team twice: once as the home team and once as the away team. Generate both A vs B and B vs A, but do not generate self-matches.

SELECT
    a.team_id   AS team_1_id,
    a.team_name AS team_1_name,
    b.team_id   AS team_2_id,
    b.team_name AS team_2_name
FROM teams AS a
JOIN teams AS b
#####     ON a.team_id <> b.team_id
ORDER BY
    a.team_id,
    b.team_id;


#### Q3. Generate the complete match schedule where every team plays every other team twice: once in the first direction, nce in the reverse direction. Add a match_number column: First meeting between the two teams: 1, Reverse meeting: 2

<img width="1062" height="675" alt="image" src="https://github.com/user-attachments/assets/242f62ff-c198-42ec-b4a0-c447dc834207" />


SELECT
    a.team_id AS team_1_id,
    a.team_name AS team_1_name,
    b.team_id AS team_2_id,
    b.team_name AS team_2_name,
    CASE
        WHEN a.team_id < b.team_id THEN 1
        ELSE 2
    END AS match_number
FROM teams a
JOIN teams b
#####     ON a.team_id <> b.team_id
ORDER BY
 #####    LEAST(a.team_id, b.team_id),
 #####    GREATEST(a.team_id, b.team_id),
 #####    match_number;
