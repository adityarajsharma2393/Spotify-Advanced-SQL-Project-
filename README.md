Project Steps
1. Data Exploration
The dataset contains the following key attributes:

Artist: The performer of the track.
Track: The name of the song.
Album: The album to which the track belongs.
Album_type: The type of album (single or album).
Other metrics include danceability, energy, loudness, tempo, and more.

2. Database Setup
Create a database structure to store the data using the following schema:

DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);

3. Data Insertion
Once the database is set up, insert the data from the provided dataset into the spotify table for querying.

4. Querying the Data
##Easy Queries
Retrieve the names of all tracks with more than 1 billion streams.
SELECT track FROM spotify WHERE stream > 1000000000;

List all albums along with their respective artists.
SELECT DISTINCT album, artist FROM spotify;

Get the total number of comments for tracks where licensed = TRUE.
SELECT SUM(comments) FROM spotify WHERE licensed = TRUE;

Find all tracks that belong to the album type single.
SELECT track FROM spotify WHERE album_type = 'single';

Count the total number of tracks by each artist.
SELECT artist, COUNT(track) FROM spotify GROUP BY artist;

##Medium Queries
Calculate the average danceability of tracks in each album.
SELECT album, AVG(danceability) FROM spotify GROUP BY album;

Find the top 5 tracks with the highest energy values.
SELECT track, energy FROM spotify ORDER BY energy DESC LIMIT 5;

List all tracks with their views and likes where official_video = TRUE.
SELECT track, views, likes FROM spotify WHERE official_video = TRUE;

For each album, calculate the total views of all associated tracks.
SELECT album, SUM(views) FROM spotify GROUP BY album;

Retrieve the track names that have been streamed more on Spotify than on YouTube.
SELECT track FROM spotify WHERE stream > views;

##Advanced Queries
Find the top 3 most-viewed tracks for each artist using window functions.
SELECT artist, track, views
FROM (
    SELECT artist, track, views,
    ROW_NUMBER() OVER (PARTITION BY artist ORDER BY views DESC) AS rank
    FROM spotify
) AS ranked_tracks
WHERE rank <= 3;

Find tracks where the liveness score is above the average.
SELECT track, liveness FROM spotify WHERE liveness > (SELECT AVG(liveness) FROM spotify);

Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.
WITH energy_diff AS (
    SELECT album, MAX(energy) AS max_energy, MIN(energy) AS min_energy
    FROM spotify
    GROUP BY album
)
SELECT album, max_energy - min_energy AS energy_difference
FROM energy_diff;

Find tracks where the energy-to-liveness ratio is greater than 1.2.
SELECT track FROM spotify WHERE energy_liveness > 1.2;

Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.
SELECT track, views, likes, SUM(likes) OVER (ORDER BY views) AS cumulative_likes
FROM spotify;

5. Query Optimization
Initial Query Performance Analysis Using EXPLAIN
Before optimization, we analyzed the query's performance using the EXPLAIN function, focusing on the artist column.

Execution time: 7 ms
Planning time: 0.17 ms
Index Creation on artist Column
To improve performance, an index was created on the artist column:

CREATE INDEX idx_artist ON spotify(artist);
Performance Improvement

After optimization, the query performance improved significantly.

Execution time: 0.153 ms
Planning time: 0.152 ms
