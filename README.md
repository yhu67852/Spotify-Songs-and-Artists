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


## Data Cleaning and Exploratory Data Analysis

### Handling Missing Audio Features (`tempo`)

The first thing I did was drop around 22,000 rows where `tempo`, `artists`, or `track_name` were missing. 

Then, I realized, through a permutation test, that the missingness of `tempo` was highly dependent on `acousticness`, which made sense because tracks with high acousticness (like classical orchestral recordings, rain sounds, etc.) lack a drumbeat, which caused the algorithm to fail to detect a Beats Per Minute (BPM). 

By dropping those tracks, I restricted the dataset to "structured" music. This means that the conclusions only apply towards traditional, rhythmic music. 


### String Splitting and Explosion (`artists`) 

The `artists` column contained strings of multiple artists separated by semicolons (e.g. `"Ingrid Michaelson;ZAYN"`). I converted these into a Python list and used the .explode() function to separate them. 

Splitting these strings allowed for the two datasets to be merged into one because the `artists.csv` dataset did not include "Ingrid Michaelson;ZAYN" as a single entry. 


### Deduplication for Primary Artist Mapping

After exploding the dataset, I used `drop_duplicates(subset=['track_id'])` to keep only the *first* artist of each track because the first artists listed is generally the "Lead Artist" while subsequent artists are "Featured Artists". The Lead Artist's popularity generally drives the popularity of a track. 

By dropping the duplicates, it made the paired t-test to be more accurate. Without dropping duplicates, our sample size would have double-counted collaborative hits. 

### The Inner Merge

I performed an `inner` merge between our cleaned `music_tracks.csv` and `artists.csv`. This allowed for any artists with unknown follower counts to be dropped. Because `followers` was the core independent variable, keeping tracks with null followers would have ruined the model. 

### The Cleaned DataFrame

Below is the `head()` of the cleaned DataFrame: 

"| track_name                 |   artist_popularity |   followers |   track_popularity |\n|:---------------------------|--------------------:|------------:|-------------------:|\n| Comedy                     |                  66 |     852,637 |                 73 |\n| Ghost - Acoustic           |                  53 |      11,874 |                 55 |\n| To Begin Again             |                  68 |     722,496 |                 57 |\n| Can't Help Falling In Love |                  71 |     438,860 |                 71 |\n| Hold On                    |                  70 |      99,345 |                 82 |"
