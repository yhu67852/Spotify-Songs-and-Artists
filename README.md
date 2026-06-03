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

| track_name                 |   artist_popularity |   followers |   track_popularity |
|:---------------------------|--------------------:|------------:|-------------------:|
| Comedy                     |                  66 |     852,637 |                 73 |
| Ghost - Acoustic           |                  53 |      11,874 |                 55 |
| To Begin Again             |                  68 |     722,496 |                 57 |
| Can't Help Falling In Love |                  71 |     438,860 |                 71 |
| Hold On                    |                  70 |      99,345 |                 82 |


### Univariate Analysis: Track Popularity

<iframe
  src="assets/track_popularity_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This interactive histogram displays the distribution of track popularity scores across the dataset, revealing a roughly normal distribution centered in the 30-50 range, meaning most songs achieve only moderate success. However, there is a massive, distinct spike at exactly zero, highlighting the harsh reality of the music industry where a significant volume of tracks remain entirely undiscovered or unplayed by the platform's algorithm.

### Bivariate Analysis: The Fame Multiplier

<iframe
  src="assets/artist_vs_track.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This 2D density heatmap reveals a clear positive trend between an artist's overall popularity and popularity of their tracks. Because the data is upward-sloping, it shows that popular artists almost never release tracks with near 0 popularity, regardless of its acoustic dataset. 

### Aggregate Analysis: Features by Top Genres

To understand how audio features vary across different styles of music, I grouped the dataset by the top 10 most frequent genres and calculated their mean acoustic features and popularity scores.

| track_genre       |   danceability |   energy |   valence |   popularity |
|:------------------|---------------:|---------:|----------:|-------------:|
| pop-film          |          0.597 |    0.605 |     0.529 |       59.283 |
| pop               |          0.630 |    0.606 |     0.506 |       47.576 |
| progressive-house |          0.624 |    0.813 |     0.365 |       46.615 |
| piano             |          0.455 |    0.320 |     0.313 |       45.273 |
| pagode            |          0.578 |    0.712 |     0.688 |       44.298 |
| acoustic          |          0.550 |    0.435 |     0.424 |       42.483 |
| punk-rock         |          0.507 |    0.810 |     0.567 |       38.236 |
| power-pop         |          0.473 |    0.802 |     0.615 |       26.898 |
| opera             |          0.314 |    0.317 |     0.215 |       24.621 |
| party             |          0.667 |    0.871 |     0.681 |       20.982 |

**Significance of this Table:** This grouped table is highly significant to our core hypothesis because it proves that "sounding fun" does not automatically equate to "being popular." For example, the `party` and `power-pop` genres have the highest average `energy` and `danceability` metrics in the dataset, yet they have the lowest average popularity scores, reinforcing the conclusion that intrinsic audio features are poor standalone predictors of commercial success.


## Assessment of Missingness

### NMAR Analysis
In this dataset, the `album_name` column contains missing values that are likely Not Missing At Random (NMAR). 

This is because some tracks, released as singles, do not belong to any albums, thus leading to the track's album column being blank. Because the missingness is now dependent on the release strategy, it is NMAR. 

If there was another column about the type of release of the song (e.g. 'EP', 'Single', 'Live', 'Album), the missingness would go from NMAR to Missing at Random (MAR). This way, the missingness of `album_name` would be dependent on the new column of `release_type`. 


---

### Missingness Permutation Tests
The `tempo` column contains approximately 22,000 missing values. To understand if this missingness was dependent on other features in the dataset, I ran two permutation tests (using 500 simulations and a significance level of $\alpha = 0.05$).

1. **Test 1: Does the missingness of `tempo` depend on `danceability`?**
    * **Result:** The $p$-value was roughly $0.40$. We fail to reject the null hypothesis. The missingness of `tempo` does **not** depend on a track's danceability.
2. **Test 2: Does the missingness of `tempo` depend on `acousticness`?**
    * **Result:** The $p$-value was $0.000$. We reject the null hypothesis. The missingness of `tempo` is highly dependent on a track's acousticness.

<iframe
  src="assets/missingness_test.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

**Interpreting the Results:** The interactive plot above shows the empirical distribution of the test statistic (the absolute difference in mean `acousticness`) under the null hypothesis. The red dashed line represents our actual observed statistic. 

Because our observed statistic falls entirely outside the realm of random chance (far to the right of the gray distribution), we confidently conclude that `tempo` is missing systematically based on acousticness. Conceptually, this makes perfect sense for our overarching question: highly acoustic tracks (like classical symphony recordings or ambient sleep sounds) often lack a rigid digital drumbeat, causing the algorithm to fail to detect a Beats Per Minute (BPM), resulting in a missing `tempo` value.