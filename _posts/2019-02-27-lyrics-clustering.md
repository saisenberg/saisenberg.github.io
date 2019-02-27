---
title: "Lyrics, Pt. 3: Rap Song Clustering with Doc2Vec"
date: 2019-02-27
permalink: /projects/lyrics-clustering.html
excerpt: "Also included: topic modeling, t-SNE, and a whole lot of scatterplots."

---
<hr>
In the [first installment](https://saisenberg.com/projects/lyrics-classifier.html) of my lyrics project, we scraped thousands of song lyrics from [*Genius*](https://genius.com/), and created a set of classifiers which identify song genres from their lyrical content. [Part two](https://saisenberg.com/projects/songwriterbot.html) of the project introduced the world to [*SongwriterBot*](http://songwriterbot.herokuapp.com), a fully functional web application that uses Markov chaining to randomly generate entire sets of song lyrics. (Special thanks to *r/hiphopheads* for the [love](https://www.reddit.com/r/hiphopheads/comments/acwky9/original_python_program_that_writes/)!) Now, in the final installment of the Great Lyrics Trilogy, I'll once again focus on the contents of the lyrics themselves to determine artist-to-artist similarity, genre-to-genre similarity, and inner/intra-artist song topics.
<hr>
We'll start by examining a pair of topic modeling techniques, both of which we'll use to extract sets of key terms from the lyrics of our selected artist(s). Each set of terms will comprise a "topic" for that artist (or group of artists). Generally, [*20 newsgroups*](https://scikit-learn.org/0.19/datasets/twenty_newsgroups.html) is the most popular dataset for topic modeling, with the terms of different extracted topics corresponding with various news categories, such as politics, sports, or technology. We'll follow a similar approach with our dataset of song lyrics.

If you're interested in following along, I'll be sharing snippets of code as I describe each step in the process. Full code, along with a sample set of lyrics, are available on my [GitHub](http://github.com/saisenberg/lyrics-clustering).

Let's initialize a *LyricsAnalyzer* object, which we'll use to store and process our set of lyrics. For additional details on the *LyricsAnalyzer* class, I recommend checking out the *.py* file of the same name. Our object will include lyrics from the late Lil Peep, an "emo rap" pioneer who was best known for mixing themes from pop-punk and rap, as well as somber lyrics about heartbreak, death, and various narcotics.

First, let's enter the *params.py* file and update our artist dictionary accordingly. (Make sure to also change your path parameters as appropriate.)

```

artist_dict = {
  'country':[],
  'metal_rock':[],
  'pop':[],
  'rap':[],
  'soul':[]
}

```

In *run.py*, we can now easily prepare our Lil Peep data frame and initialize our *LyricsAnalyzer* object:

```

# Prepare data frame with lyrics from specified artists
lyrics_df = prepareLyricsFrame(artist_dict = artist_dict, path = corpus_path)

# Create LyricsAnalyzer object
la = LyricsAnalyzer(lyrics_df)

```

Upon initialization of our *LyricsAnalyzer* object, there are a few important behind-the-scenes methods - TF-IDF and Count vectorization - that automatically act on our set of lyrics. TF-IDF collects a list of every term that appears in *any* of our selected songs (note that "terms" are not necessarily limited to one word), and counts how often each term appears in each song. It then re-weights every frequency value according to the number of documents in which the associated term appears. Words that appear in virtually every song ("the", "and", "or", etc.) will be down-weighted, and vice-versa.

While not shown in the above code, note that there are optional parameters, *min_df* and *max_df*, which are available in our *LyricsAnalyzer* initialization. Both parameters affect the TF-IDF vectorization that occurs upon initialization; the parameters' defaults, which are set to 0.03 and 0.60, respectively, ensure that the resulting matrix will ignore terms which appear in less than 3% or greater than 60% of songs. This speeds up processing time for larger sets of lyrics, but also helps prevent topics from being "overtaken" by words which only appear in a couple songs. I recommend playing around with *min_* and *max_df* values for yourself, as various sets of values might work better for certain artist combinations.

The *LyricsAnalyzer* object also initializes with a CountVectorized matrix, which, aside from the re-weighting described above, is identical to TF-IDF matrix. We will use the TF-IDF matrix for NMF (or *non-negative matrix factorization*), and the CountVectorized matrix for LDA (or *latent Dirichlet allocation*), respectively.

By calling the *get_nmf_topics* and *get_lda_topics* methods, we're able to extract the top words for as many topics as we request. It's important to remember that for both techniques, *we're* the ones who choose the number of topics to extract - and our choices will have a significant impact on the extracted topics. Picking too few topics might result in each topic being overly vague and unfocused, while choosing too many will stratify our topics too narrowly. I've generally had the most success with somewhere between roughly seven and ten topics, but I recommend experimenting for yourself. Here, I'll be using eight topics for NMF, and seven for LDA.

```

# Collect the top N topics (LDA)
lda_topics = la.get_lda_topics(n_topics=8)

# Collect the top N topics (NMF)
nmf_topics = la.get_nmf_topics(n_topics=8)

```

Let's take a look at Lil Peep's topics:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-clustering/images/topics_lil_peep.png" alt="Lil Peep topics"></center><br>

NMF's first two topics are clearly both about the rapper's romantic partners, with the first topic appearing far more romantic than the second. Topics 3 and 6, on the other hand, seem to deal with death and drugs, respectively, and Topic 5 appears vaguely associated with heartbreak. The topics for LDA are a bit more ambiguous than NMF's, but we can still see similarly themed collections of terms appear in each topic.

Let's repeat this exercise with some other rappers. After adding the discographies of 21 Savage and Tyler, The Creator - both of whom have relatively unique styles - we'll now collect only *three* NMF topics, rather than the eight used above. Theoretically, we'd expect NMF to recognize and distinguish each rapper's style from one another, and treat each one as its own topic. In order to more fully understand each topic, I've also elected to showcase the top twenty terms per topic.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-clustering/images/topics_lil_peep_tyler_21.png" alt="Lil Peep, 21 Savage, Tyler, The Creator topics"></center><br>

Just as we hoped it would, NMF has perfectly identified each artist as his own topic. In order, the three topics above clearly represent Lil Peep, 21 Savage, and Tyler, The Creator. Of course, note that NMF does not label any topics *for* us - it's up to us to interpret the underlying "meaning" of each one.
<hr>
Finally, I've also built a Doc2Vec method into the *LyricsAnalyzer* object, with which we'll create document "embeddings" for every set of lyrics in our dataset. Doc2Vec is an extension of Word2Vec, which, from a broad standpoint, uses a neural network to turn words into *n*-length vectors based on the words which most frequently surround them. Doc2Vec, on the other hand, outputs *n*-length vectors for entire *documents*, rather than for individual words.

Running Doc2Vec on our set of lyrics is very simple. I'll be using the default parameters for vector length and window, among other parameters, but you can adjust these as you see fit. The default vector length is three-hundred - on the outskirts of the common range of one- to three-hundred. The method will return to us a series of vectors, each one of length three-hundred, with every vector corresponding to a different song.

```

la.doc2vec()

```

For purposes of more efficient song-to-song comparison, we'll use [t-SNE](https://distill.pub/2016/misread-tsne/) to condense our three-hundred column dataset into one with just two features. This is not an exact exercise, and post-t-SNE results are meant to be taken only as an approximation. While distances between groups of points are meaningful, they should not be taken literally. Further, we should not draw any conclusions from a point's placement on the *x*- or *y*- axis; song positions are only meaningful when examined in relation to other songs on the same plot.

```

la.tsne_from_d2v(return_df=False)

```

After writing the resulting image to a .*csv* file, we can hop into *R* to visualize our results. I've colored the data points by artist and manually annotated some of the more interesting song placements. Let's see what Doc2Vec made of our lyrics collection:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-clustering/images/labels-lilpeep_tyler_21.png" alt="Lil Peep, 21 Savage, Tyler, The Creator labeled plot"></center><br>

Look at how precisely Doc2Vec segregated each artist! While a few songs fall across the lyrical "boundaries" of other artists, each rapper in our dataset has a distinct section of the plot. Keep in mind that Doc2Vec had absolutely no knowledge that our dataset had multiple artists; in fact, we never passed any artist data into the model at all! The model simply recognized that certain words occurred most often in the presence of others, and created a series of resulting vectors that so happened to perfectly segment our data across artists. It's also noteworthy which songs have lyrics that resemble songs from other artists. For instance, 21 Savage's "*A Lot*" apparently bears a stronger resemblance to Lil Peep lyrics than to the rest of 21 Savage's discography.

Repeating this process with select groups of artists can give us some pretty interesting results. Let's see, for example, if songs from the individual members of mid-2010s rap collective Odd Future separate out as cleanly as the above example:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-clustering/images/plot-oddfuture.png" alt="odd future plot"></center><br>

Aside from Tyler, The Creator and Earl Sweatshirt - both of whose clusters are, at best, ill-formed - there's not much evidence of significant lyrical differences among Odd Future members. Each rapper's respective data points are thoroughly scattered throughout the plot, suggesting that while Tyler's lyrics might heavily diverge from those of Lil Peep and 21 Savage, they remain relatively similar to his Odd Future compatriots.

I'm compiled some more fun clustering examples below. Even better, I haven't limited my search to just rappers, either - you'll see comparisons involving pop artists, rock bands, and more! When appropriate, I've labeled particularly noteworthy data points in the same manner as above.

[**See all images here!**](https://imgur.com/a/FBGa9CB)

In general, it looks like rap songs tend to cluster by artist better than songs from other genres. My hunch is that this phenomenon, at least in part, is due to many rappers having distinct catchphrases or adlibs that they reuse from song to song. Country, pop, and rock artists generally don't follow this trend, and certain genres (I'm looking at you, country) are often chastisted for what is perceived as redundant lyrical content between different artists.

I had a great time putting this project together, and I hope you enjoyed it too! All code is available on my [GitHub](http://github.com/saisenberg/lyrics-clustering).
