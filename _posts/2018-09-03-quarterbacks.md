---
title: "Assigning Success Likelihoods to Recently Drafted Quarterbacks"
date: 2018-09-03
permalink: /projects/quarterbacks.html
excerpt: "Many NFL teams struggle to develop franchise quarterbacks. Can machine learning predict which recent draftees will succeed in the pros?"

---
<hr>
As long as the National Football League has been in existence, teams have attempted (and, for the most part, failed) to predict college quarterbacks’ respective potentials for success in the league. Some teams have had virtually no gap between franchise quarterbacks – the Green Bay Packers, for instance, have seen Hall of Famer Brett Favre immediately succeeded by future Hall of Famer Aaron Rodgers – while others, such as the woebegone [Browns](http://www.espn.com/espnw/sports/article/16371464/owner-infamous-browns-qb-name-jersey-retire-it), have struggled at this task for decades.

The quarterback, of course, is the league’s most important position, and teams rarely win the Super Bowl without a star quarterback leading their offense. With the exception of Nick Foles (who only started Super Bowl LII due to an ACL injury to then-MVP candidate Carson Wentz), very few non-star quarterbacks – these non-star players including Trent Dilfer and Brad Johnson – have been able to take home a Lombardi Trophy. Identifying the college quarterbacks most geared for professional success is clearly a crucial undertaking for NFL teams.

But what does “success” actually mean? It is easy, after all, to classify Dilfer or Johnson as “successful” – they did both win the Super Bowl, after all – but should they still be considered successful if their in-season statistics were far worse than their playoff resumes would suggest? What about a quarterback like Nick Foles, who, aside from a strong 2013 campaign and miraculous 2017 playoff run, has been unextraordinary?

If we are to make an honest attempt at predicting a college quarterback’s chances at NFL success, we must first objectively define "success". In this context, I believe success is most aptly described as a combination of player *production* and *longevity*. Backup quarterbacks with good statistics in limited play (A.J. McCarron, for example), as well as players with long but unproductive careers (such as Jon Kitna), should not be categorized the same as Tom Brady or Drew Brees, as neither McCarron nor Kitna’s careers have had sufficient production and longevity. Note that for purposes of this analysis, quarterbacks who have only been in the league for a limited time, but have received the bulk of team snaps – Blake Bortles and Derek Carr being prime examples – are considered as having sufficient longevity despite their short career lengths.

My first task was to use *K-means* clustering to segment quarterbacks into groups, and to classify certain groups (or "clusters") as “successful." I utilized R’s [*htmltab*](https://cran.r-project.org/web/packages/htmltab/htmltab.pdf) library to scrape *pro-football-reference* for the statistics of all quarterbacks who made their NFL debuts during or after 1985, excluding quarterbacks who retired more than twenty years ago as well as any quarterback drafted in 2017 or 2018. Quarterbacks without at least one credited win, loss, or tie as per [*pro-football-reference.com*](https://www.pro-football-reference.com/) were also removed.

Clustering required much trial & error to answer a number of key questions:
* What number of clusters is most appropriate?
*	What variables should we cluster on?
*	Should all variables be equally weighed? If not, how much weight should be assigned to each field?

Ultimately, I settled on **nine** clusters on the following fields. Note that the default weight for each field is one (1).
*	Quarterback rating (weight of 2.5)
*	Attempts per year (1.5)
*	Adjusted yards per attempt (1)
*	Games started per year (1)
*	Win percentage (1)  

Below are the clusters visualized on a two-dimensional scale, with attempts per year (i.e., longevity) and quarterback rating (production) on the x and y-axes, respectively. The clusters are randomly ordered – cluster #1, for instance, does not correspond with the most “successful" quarterback group – but it is clear that groups #8 and #4 are the two strongest clusters.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/fig1-qbclusters.png" alt="quarterback cluster scatterplot"></center><br>

From the quarterbacks who comprise group #8, we can see that the cluster includes a significant percentage of the league’s most successful quarterbacks over the past few decades. As follows is a list of these quarterbacks:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/fig2-group8qbs.png" alt="most successful quarterbacks"></center><br>

Group #4, however, is less straightforward; while many quarterbacks in the cluster have had perfectly fine careers, they still may not be most appropriately categorized as “successful.” For instance, while classifying Tom Brady as a success is easy, doing the same for a quarterback like Sam Bradford, Mark Sanchez, or Matt Schaub – all group #4 quarterbacks with solid but unspectacular careers – is much more difficult. Cluster #4 also includes quarterbacks who, in my opinion, definitely *are* deserving of the “success” label (such as Randall Cunningham or Rich Gannon), as well as other players (including Jake Locker and Tim Couch) who clearly do *not* deserve the title.

Cluster #4 clearly required further stratification, and for this exact reason, I split this group of quarterbacks into two sub-clusters. Rather than the five-variable approach detailed above, though, this method was far simpler, using only the passing rate field. After re-scaling the cluster's passing rates - this time, separately from all other quarterbacks - players with scaled rates above zero, such as Chad Pennington and Jake Delhomme, were deemed successes, and those with scaled rates below zero, including Matt Cassel and Josh Freeman, were excluded from the category. Overall, 60 out of 240 quarterbacks (25%) in the dataset were ultimately classified as “successful.”

I should also note that although San Francisco quarterback Jimmy Garoppolo fell into neither group #8 nor #4, I felt it appropriate to deem him a success. Because Garoppolo spent nearly the entirety of his first three years backing up Tom Brady in New England – while also clearly demonstrating his ability on the field when given the chance – he was being unfairly penalized for his relatively low number of attempts per year.

Next, I scraped and aggregated four decades’ worth of college quarterback statistics from the college football section of [*sports-reference.com*](https://www.sports-reference.com/cfb/). Certain quarterbacks from smaller, non-BCS schools were missing from the website, and their statistics were individually pulled from their respective universities’ online archives. Some quarterbacks simply had no publicly available college statistics, and therefore had to be removed from the remaining steps of the analysis. Sadly, Kurt Warner and Rich Gannon (alumni of Northern Iowa and Delaware, respectively) were among this group.

Finally, I used R’s [*jsonlite*](https://cran.r-project.org/web/packages/jsonlite/index.html) library to import and clean quarterback height and weight figures (found on [*Kaggle*](https://www.kaggle.com/zynicide/nfl-football-player-stats)).

After exporting to a .*csv*, I brought the data into Python to train and test my models. I thought it most appropriate to model this data using random forests, and did so with *sklearn*’s *RandomForestClassifier*. The dataset’s relatively small sample size makes it impractical for neural network modeling, and random forests, I felt, provided more stability – and, hopefully, less bias – than a single decision tree.

As with my earlier quarterback clustering, tuning the random forest classifier required much trial & error (sometimes performed manually, sometimes using grid search) over a number of parameters. My final random forest model included approximately one thousand estimators, a maximum tree depth of five layers, and a maximum number of tree features equal to the square root of the number of predictors. In this case, the model included five predictors:
* Attempts per game
*	Height
*	Number of years in school
*	Pass/rush ratio (a quarterback’s total college pass attempts divided by his total rush attempts)
*	[Passing efficiency rating](https://www.sports-reference.com/cfb/about/glossary.html)

Because the response variable was so imbalanced, with a 75/25 quarterback failure/success ratio, I used *SMOTE*, or *Synthetic Modeling Oversampling Technique*, to oversample from the minority class before fitting the forest. Using data from the *k* nearest neighbors of the minority class – in this case, ten similar successful quarterbacks – *SMOTE* creates a series of “synthetic” data points from the feature vectors of those nearest neighbors. In this instance, I used *SMOTE* to install enough new synthetic successful quarterbacks to turn the 75/25 distribution into a perfectly equal one.

The random forest proved to be quite successful on the oversampled dataset, as the model predicted quarterback outcomes – both authentic and synthetic – with roughly 91% accuracy. Out of 360 total observations, 120 of which were created using *SMOTE*, less than thirty were misclassified. Below is the resulting confusion matrix:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/cm-training.png" alt="training confusion matrix"></center><br>

We still, however, have to make sure the model is generalizable to data it has yet to see. Consequently, I excluded 24 quarterbacks from the original training dataset (i.e., the dataset *without* any synthetic observations), and used these quarterbacks to test the fitted random forest. Results are illustrated below:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/cm-test.png" alt="test confusion matrix 1"></center><br>

The model was still fairly accurate on the test set! As shown above, 20 of the 24 quarterbacks, or 83%, were correctly predicted, which – while less than the 91% accuracy rate of the synthetic set – is still a strong result. (For those curious, the model accurately classified Dak Prescott, Steve McNair, Daunte Culpepper, and Carson Wentz as successes, incorrectly classified Ryan Fitzpatrick and Quincy Carter as successes, and incorrectly classified Joe Flacco and Drew Bledsoe as failures.) The 83% accuracy rate is also significantly higher than the 75% accuracy rate achievable simply by guessing “failure” for every quarterback.

Of the five features used to “grow” the random forest, we can also see which were most useful in decreasing tree impurity. As follows are the feature importances of each variable, ordered by importance:
*	Height: 25.2% importance
*	Passing efficiency rating: 23.5%
*	Attempts per game: 19.9%
*	Years in school: 16.3%
*	Pass/rush ratio: 15.0%

Perhaps the most important application of the model, however, is observing its response to quarterbacks drafted within the past couple of years. For notable quarterbacks taken in the 2017 and 2018 NFL drafts, I have collected the model’s predictions below, and, rather than simply listing binary success/failure predictions, have instead included all likelihood percentages of success. For purposes of a rough success/failure prediction, I have classified any quarterback with a success likelihood in excess of 55% as a success, any quarterback with a likelihood lower than 45% a failure, and any quarterback with a likelihood between the two as a “toss-up”.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/success-failure-tossup-1.png" alt="success likelihood chart 1"></center><br>

Interestingly, these results largely appear to follow general consensus. Recent draftees Deshaun Watson, Sam Darnold, and Lamar Jackson are viewed as high-potential players, and players like Nathan Peterman and C.J. Beathard – both of whom already have some (rather dismal) professional experience – are seen as failures. Curiously, DeShone Kizer, who has also struggled in his brief NFL playing time, is projected as a quarterback more likely to succeed than to fail, and 2018 #1 draft pick Baker Mayfield was given the lowest success probability in the bunch – even lower than fourth and fifth-round picks Kyle Lauletta and Peterman, respectively. Also of note is Josh Allen and Mitch Trubisky, two other top picks in the 2018 draft class, both of whom were projected as toss-ups rather than successes.

Before we celebrate a job well done, though, it is important to make sure that our 83% test accuracy was not purely achieved through chance. Re-fitting the model with different training and test datasets might result in different results; what happens when we change our *train_test_split* seed?

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/cm-test-2.png" alt="test confusion matrix 2"></center><br>

The results here are weaker than those of the initial test confusion matrix. Less than 67% of this test set’s quarterbacks were correctly predicted, which is below the “no knowledge” rate of 75%. However, the success likelihoods of the recently drafted quarterbacks were fairly stable – more so than the overall accuracy rate. Compare the first set of likelihood results to the second set; although a few quarterbacks’ percentage likelihoods had dramatic changes, virtually all quarterbacks who were originally projected as a strong success or failure keep those same projections:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/success-failure-tossup-2.png" alt="success likelihood chart 2"></center><br>

Quarterbacks like Mayfield, Peterman, and Beathard, all of whom the model initially predicted to fail, are still projected to do so. Mayfield’s success likelihood increased by eleven percentage points, but he remains firmly in the “failure” category. Conversely, Watson and Sam Darnold still receive two of the highest success likelihoods, although both Kizer and Josh Rosen jump Darnold’s 73.7%.

The above chart, though, only reflects two random train/test splits, and some quarterbacks’ likelihoods vary much more wildly than Watson’s or Darnold’s. If two models, both fit on randomly split training data, can predict likelihoods seventeen percentage points apart for Josh Allen, how can we really know whether Allen’s likelihood rests closer to 49% or to 66%? If we repeated this process much more than twice – say, *ten thousand* times – we can obtain a much better sense of each quarterback’s true predicted success likelihood.

After ten thousand iterations of random forest training, testing, and predicting, we are left with a series of likelihood distributions – one for each recently drafted quarterback – that provide us with far more certainty regarding the success probabilities for each player. As follows are the mean success likelihoods for each quarterback:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/success-failure-tossup-10K.png" alt="success likelihood chart 10,000"></center><br>

Note that the quarterbacks’ mean projections are, for the most part, relatively close to their earlier success likelihoods. Deshaun Watson’s mean projection of 75%, for example, is only slightly different than his first two success likelihoods of 75.6% and 78.7%. Lamar Jackson and Cooper Rush, on the other hand, see mean success likelihoods – approximately 57% and 43%, respectively – that are significantly lower than their results from their first two iterations. Therefore, running the model ten thousand times, while time-consuming, was clearly worthwhile, as we would have otherwise come away with very different impressions of certain players.

We can also plot the distributions of each quarterback’s ten thousand success likelihoods, all of which resemble normal distributions. Compare histograms of the distributions for C.J. Beathard and Mitch Trubisky below, both plotted using twenty-five bins on the x-axis:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/dist_beathard_trubisky.png" alt="beathard trubisky success distributions"></center><br>

Not only does Beathard’s distribution fall much farther to the left than that of Trubisky – which stands to reason, given that Beathard’s mean success likelihood is thirty percentage points lower than Trubisky’s – but Beathard’s histogram is also much narrower. This indicates that the model is far more “certain” about Beathard’s success (or lack thereof) than it is about Trubisky’s. We can also use each distribution’s standard deviation as a numeric measure of model certainty; while Trubisky’s distribution exhibits a standard deviation of 8.2%, Beathard’s is just 4.5%, again indicating the differences in model certainty surrounding each player.

Below, I’ve plotted every quarterback’s average success likelihood against the variability in their respective projects:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/mean_projection_vs_variability.png" alt="qb projections against variability"></center><br>

We can see that quarterbacks with mean projections close to 50% - such as Trubisky and Rush – are also those with the most variable projections. On the other hand, quarterbacks closer to either end of the success/failure spectrum tend to have tighter prediction windows.

Lastly, it is crucial to remember that mean success likelihood is only one method by which to measure model predictions – and there may be better ways to do so. Rather than using quarterbacks’ average projections to categorize them as successes, failures, or toss-ups, we can instead group each player’s results into ten-percentage-point bins, and examine what *percentage* of outcomes fell into each bin.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/quarterbacks/5bins.png" alt="qb success bin results"></center>

<small><small><center>To view full-size image, right-click and select "Open image in new tab."</center></small></small>

We now have a much more detailed understanding of player projections than we would by simply using their mean result. Further, while the above chart is conceptually similar to the histograms discussed earlier, this chart is far easier to interpret.

A few interesting items of note:
* The model is substantially more optimistic about Deshaun Watson than any other quarterback. Watson is the only quarterback to receive more than 1% of predictions in the 80-85% success range (receiving nearly *18%* in the range!), and is the only quarterback to receive *any* success likelihood results of above 85%.
* Although DeShone Kizer’s mean projection is slightly higher than that of Patrick Mahomes, the model has considerably less certainty concerning the latter. As a result, while both quarterbacks are most often projected as successes, Mahomes receives both a lower floor and higher ceiling than Kizer.
* Despite playing in different conferences and achieving vastly different statistics in college, Kyle Lauletta and Nathan Peterman are given virtually identical success likelihood distributions.

Even in retrospect, judging the accuracy of these sorts of models is difficult. DeShone Kizer, as mentioned above, is nearly always projected as a successful quarterback – less than 7% of his results fall in the “toss-up” category, and none fall in the “failure” category – but his real-life play has been anything but successful. How much of this, though, can we attribute to Kizer, and how much can we attribute to the situation into which he was drafted? It is entirely possible that Kizer entered the league with a skill set well-suited for success, but a struggling and tumultuous Cleveland Browns organization hindered his development. Conversely, while the model appears correct regarding Deshaun Watson, it could be that the exact opposite (albeit, hypothetical) phenomenon has occurred for Watson as for Kizer.

Going forward, I would like to continue to improve this model – first and foremost by improving its training & test accuracy on quarterbacks whose outcomes we already know. This can be done by more finely tuning the random forest algorithm, adjusting the *SMOTE* oversampling methodology, by using a different classification technique (such as gradient boosted trees), or with some combination of the three. Additionally, I can continue mixing and matching the features used in the model, as well as creating new ones, in order to improve its accuracy. Ideally, creating a more accurate and stable model will result in less variable – and more trustworthy – projections for recent draftees.

In its current state, though, the model still has utility as it relates to projecting top draft prospects and recently drafted quarterbacks. While analytics may not be an all-encompassing solution to NFL teams’ quarterback woes, the fact that many teams continue to struggle in this area represents the growing opportunity for using data-driven approaches to solve some of the sport’s biggest questions – this model being one example.

All data and code are available on my GitHub: [https://github.com/saisenberg](https://github.com/saisenberg/quarterbacks)
