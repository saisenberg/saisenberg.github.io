---
title: "Comparing Song, Album, and Artist Metrics with Spotify’s API"
date: 2019-05-18
permalink: /projects/spotify-data-comparator.html
excerpt: "An interactive dashboard that visualizes (and downloads!) data of musical characteristics."

---
<hr>
[**Click here**](http://spotify-data-comparator.herokuapp.com/) to go straight to the interactive dashboard!
<hr>
In a continuation of my recent music-based projects, I decided to dive into the Spotify API to collect and compare data of different songs, albums, and artists. Using the Dash visualization tool, I put together an interactive dashboard that lets users compare nearly 700 unique artists and over 110,000 songs on a variety of metrics. In addition, the application allows for the quick and efficient downloading of any artist’s raw data – regardless of whether that artist is among the initial seven hundred.

Each of the dashboard’s seven metrics ranges from 0 to 1. As found within Spotify’s [API documentation](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/), the metrics consist of the following. More information about each metric can be found at the preceding link.

* Acousticness: Whether the track is acoustic
* Danceability: How suitable a track is for dancing, based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity
* Energy: A measure of intensity and activity, based on perceptual features including dynamic range, perceived loudness, timbre, onset rate, and general entropy
* Instrumentalness: Whether the track contains no vocals
* Liveness: Whether the track was performed live
* Speechiness: Detects the presence of spoken words in a track
*	Valence: A measure of musical positivity conveyed by a track

One note about metric aggregation (i.e., combining song metrics into album or artist metrics): to mitigate the potentially undue influence of an interlude, album introduction, or very short track, every aggregated value has been *weighted* based on song length. Long songs, therefore, are given slightly more importance than short ones in the context of a full album or artist discography. In addition, albums whose metadata is missing a release month and day are assumed to have been released on June 15th of the labeled year.

Hope you guys enjoy! All data and dashboard code can be found on my [GitHub](https://github.com/saisenberg/spotify-data-comparator), and the dashboard itself can be found [here](http://spotify-data-comparator.herokuapp.com).
