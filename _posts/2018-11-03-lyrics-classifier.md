---
title: "Lyrics, Pt. 1: Genre Classification"
date: 2018-11-03
permalink: /projects/lyrics-classifier.html
excerpt: "Can machine learning identify a song’s genre solely from its lyrical content?"

---
<hr>
In today’s day and age, we’re seeing more crossover than ever between musical artists of different genres. Rappers regularly feature in pop songs heard on the radio nationwide, and soul singers often perform the choruses of rap songs. Heck, renowned rock musician Paul McCartney [teamed up](https://www.youtube.com/watch?v=kt0g4dWxEBo) with Kanye West and Rihanna for an unexpected 2015 collaboration.

In the first installment of this project, I’ll be building a model that predicts a song’s genre based solely on its lyrical content.
<hr>

As per usual, my first step in the modeling process was data collection and cleaning. Aggregating a full, tidy set of song lyrics isn’t the easiest task; many websites discourage web scrapers from crawling their pages – I may have gotten my IP banned from [*azlyrics*](azlyrics.com) last year for exactly this infraction – and some lyrics sites are rife with typos and errors.

First, though, I wrote a few small web scrapers to collect the names of artists in six different musical genres – **country**, **metal**, **pop**, **rap**, **rock**, and **soul** – from [*Billboard*](billboard.com), [*Ranker*](https://ranker.com), and [*TheTopTens*](https://www.thetoptens.com).

Next, I used Ewen Henderson’s *geniusR*(https://cran.r-project.org/web/packages/geniusr/geniusr.pdf) package to scrape lyrics straight from [*Genius*](http://genius.com) (formerly known as *RapGenius*) and create a separate corpus of lyrics for each genre. *Genius* hosts lyrics for a relatively wide array of musicians, and I’ve found that the site, for the most part, keeps their lyrics relatively tidy.

I wrote a *collectGenre* function which, given a list of genre artists and a few additional parameters, will randomly scrape the lyrics of artists belonging to that genre. To ensure a reasonable variety of artists within each corpus, I scraped a maximum of 75 songs per artist. Finally, I discarded any result with a title that includes the phrases “album art”, “tracklist”, “script”, or “interview”, since *Genius*’s lyrics occasionally include transcripts of additional, non-song artist materials.

The below picture demonstrates this function in use; here, *collectGenre* has been set to scrape 1,500 rap songs of character lengths between 1,750 and 4,500:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-classifier/collectGenre.png" alt="collectGenre function"></center><br>

To prepare each genre corpus for predictive modeling, I brought the scraped lyrics into Python and performed the following steps:

* **Remove misclassified artists**: Some artists – particularly country and soul musicians – do not yet have their lyrics listed on *Genius*. Consequently, the function will erroneously return lyrics from the first-listed artist in the search results, whether or not that artist actually exists within the appropriate genre. Nearly 75 Tyler, The Creator songs, for instance, mistakenly appeared in the dataset of country songs, and had to be manually removed.
* **Remove dual-genre artists**: I found that the model was being confused by a few specific artists who, while initially classified as pop, could just as easily be classified as another genre. Chris Brown, for instance, really fits as a pop, rap, and soul artist, and Shania Twain is just as much a pop artist as she is a country singer.
*	**Clean all text**: This includes (a) removing everything between hard brackets, such as “[Verse 1]”; (b) changing certain types of slang, such as “walkin’” to “walking”; (c) removing all shorthand for repetition of a certain line, such as “x4”; (d) standardizing different forms of apostrophe; (e) elongating all contractions; and (f) removing all new lines (“\n”) and punctuation.
* **Stem words**: Stemming words refers to transforming every word to its base form. This way, there will be no distinction between the words “operation” and “operative”, since both will be converted to “oper”.
* **Combine metal & rock**: I found there not to be enough of a distinction between metal and rock lyrics to justify splitting the two. All models performed the best after having combined the genres into one category.

I started with a Multinomial Naïve Bayes model, which classifies a song based on the conditional probabilities of every specific word in a set of lyrics appearing in a song of a given genre. The model had a cross-validation score of 71%, which, while fair, can definitely be improved upon. In particular, the model had the most trouble classifying pop, country, and soul songs, which had test F1-scores of just 0.56, 0.63, and 0.64, respectively. Rap songs, on the other hand, were extremely accurate, with an F1-score of 0.90.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-classifier/nb_results.png" alt="Naive Bayes results"></center><br>

Next, I moved on to a support vector machine (or SVM) model. For the SVM, I created [TF-IDF](http://www.tfidf.com/) vectors from the full set of lyrics. For every song, every word’s frequency in that song is weighted by the number of other documents in which that word occurs. Word frequencies are also weighted by the length of each song’s lyrics, so that words in short songs are not given disproportionately high weights. Words which appear very often, such as “the” or “and”, will therefore receive very low weights, as while they may occur often in a given song, they’ll appear in nearly every other song as well. Therefore, TF-IDF vectorization essentially takes care of [stopwords](https://www.ranks.nl/stopwords) on its own.

After much trial and error with SVM parameters, I eventually settled on an alpha of 0.0001 and an ngram range of two. This means that consecutive two-word pairs (or “2-grams”) in a song’s lyrics are considered “vocabulary”, rather than just each individual word. These parameters resulted in a cross-validation score of 74%, a moderate but solid improvement over Naïve Bayes.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-classifier/svm_results.png" alt="SVM results"></center><br>

Finally, I used [*XGBoost*](https://xgboost.readthedocs.io/en/latest/) to run a gradient boosting classification model, which - again, after trial and error with parameters - resulted in a score of 77%, another strong improvement over Naïve Bayes and SVM. Rap was again the easiest genre for the model, with a test F1-score of 0.96, and while pop, soul, and country were still the most difficult to classify, the F1-score for pop jumped from 0.55 to 0.67. Full parameters of the model can be found at my [GitHub](https://github.com/saisenberg/lyrics-classifier).

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-classifier/xgb_results.png" alt="Gradient boosting results"></center><br>

The gradient boosting model most commonly misclassified songs as metal/rock – likely a result of there being approximately twice as many metal/rock songs as songs of any other individual genre. Only one metal/rock song, however, was misclassified as rap, and fittingly enough, [that song](https://www.youtube.com/watch?v=L49bJdUR91A) is by longtime rap-rock band Papa Roach. Additionally, there was only a single rap song – Lauryn Hill’s [*To Zion*](https://www.youtube.com/watch?v=1sQjh261rU8) – incorrectly labeled as soul (although, if you listen to the song, you'll see that it essentially *is* a soul song). Interestingly, Chance the Rapper’s [*Sunday Candy*](https://www.youtube.com/watch?v=-knXBsbZRJA) was one of only four rap songs mislabeled as country, and The Weeknd, labeled in my dataset as a pop artist, had six test songs classified as rap. Further, a few other pop songs were labeled as rap, likely because of the artists featured on those tracks; see Madonna and Nas’s 2015 collaboration, [*Veni Vidi Vici*](https://www.youtube.com/watch?v=j3HjPk-RQw8), as an example.

Just for fun, we can also test out the model on a few previously unseen sets of lyrics. I’ve found the XGBoost model much more reliable when deployed on full sets of lyrics rather than verse-long snippets, so, for the sake of space, these examples will instead utilize the SVM model. *svmLyricClassifier* is a small function I wrote which preprocesses a given set of text and uses the SVM model to predict a song genre for that text. As follows are the model’s results for songs of every genre:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/lyrics-classifier/svm_predictions.png" alt="SVM predictions"></center><br>

<small><small><center>To view full-size image, right-click and select "Open image in new tab."</center></small></small>
<hr>
As always, thank you for reading! All code is available on my [GitHub](https://github.com/saisenberg/lyrics-classifier). Part Two coming soon...
