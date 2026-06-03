# Spotify-Songs-and-Artists
This is a data science project for UCSD's DSC80 course. 
## Introduction: 
Everyday, over 100,000 new songs are uploaded to streaming platforms like Spotify. Some of these songs become global hits, played around the world for years to come, while others fade into obscurity. But what actually seperates a global anthem from an unknown song? 


Is the secret hidden in the music itself, like having the perfect mix of tempo, energy, and rhythm, or is it more related to the name of the artist who released the song? Maybe the music industry is driven by clout, where massive pop stars can release static noise and still reach number 1? 


### The Central Question

**Does a song's success depend primarily on its intrinsic audio features, or does the artist's pre-existing fame act as a "multiplier" that dictates a song's success even before it's released?**


### Why You Should Care

Have you ever been listening to your favorite band/music artist and wondered why they aren't getting the same attention that massive pop stars are getting? Similarly, have you ever been listening to top hits and wondered, *"Why is this terrible song so popular?"*. This project will quantify exactly why that happens. 


Understanding why certain songs gain more popularity than others is instrumental to music industry analysts, A&R representatives, or even just general music enthusiasts. If audio features drive popularity, record labels could "genetically engineer" the perfect song. But, if an artist's brand dominates, labels are better off investing their resources into social media marketing and growing artists' brands. By mathematically isolating the "Fame Premium" we can see exactly how much of an unfair advantage larger artists have compared to smaller independent artists. 


### The Datatset

To answer this question, I used a massive dataset containing detailed audio statistics for Spotify tracks and artists. 

Before cleaning and merging the track data with the artist data, the dataset contained **114,000 rows** (These represented 114,000 individual tracks across 114 different genres).


### Relevent Columns

Out of the dozens of availible data points, my analysis focused on the following columns: 

* **`track_popularity`** *(int)*: This was my target variable. It was a score from 0 to 100 calculated by Spotify's algorithm based on total streams and recency of those streams. 

* **`followers`** *(int)*: The total number of users following the artist who released the track. This is the primary metric for "pre-existing fame". 

* **`artist_popularity`** *(int)*: A score from 0 to 100 representing the overall success of each artist. 

* **`danceability`** *(float)*: A value from 0.0 to 1.0 describing how suitable a track is for dancing based on tempo, rhythm stability, and beat strength, 

* **`energy`** *(float)*: A value from 0.0 to 1.0 describing how intense the track is, energetic tracks feel fast, loud, and noisy (e.g. death metal has high energy while Mozart has low energy). 

* **`valence`** *(float)*: A value from 0.0 to 1.0 describing the musical positivity of each track. High valence sounds happy and cheerful, while low valence sounds sad and depressed. 

* **`loudness`** *(float)*: The overall loudness of a track in decibels (dB). 

* **`tempo`** *(float)*: The overall estimated tempo of a track in beats per minute (BPM). 

* **`explicit`** *(boolean)*: Whether or not the track contains explicit language (`True` = explicit, `False` = clean). 