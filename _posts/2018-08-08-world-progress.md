---
title: "The Corner of Healthy & Wealthy: Is the World Getting Better?"
date: 2018-08-08
permalink: /projects/world-progress.html
excerpt: "Swedish statistician Hans Rosling claimed that the world is far better than people believe. Let's see if we reach the same conclusions."

---
<hr>
On local and national news stations, it sometimes seems that all we see is negativity: political outrage, international conflict across the globe, and crime occurring in our own neighborhoods. It'd be easy to conclude, then, that the current state of the world is worse than in generations past – and, in fact, many people believe this to be true. Hans Rosling (1948-2017), a Swedish physician, statistician, and professor, compiled a simple quiz of a dozen questions, all meant to test our knowledge of the world’s socio-economic progress. For example, one question read as follows:

*In the last twenty years, the proportion of the world living in extreme poverty has:  
(A) Almost doubled, (B) Remained more or less the same, or (C) Almost halved*

Rosling gave this quiz to groups of bankers, CEOs, scientists, and politicians – with surprising results. On average, respondents got just two questions correct out of twelve – an even worse result than if every respondent had randomly guessed! Nearly everyone believed that the world was far worse than it actually was; for instance, while the correct answer to the question above is *C*, the most common choice was *A*.

In Rosling’s posthumous book *Factfulness*, he argues that the world is – contrary to popular opinion – better than ever. Further, he attributes our inability to recognize this positive change to a number of human “instincts.” One of these, the *negativity instinct*, ties in with the negative headlines that dominate our news cycles. Humans are simply more attentive to adverse events than positive ones, so it is logical that the media would place a greater emphasis on negative headlines.

One of the book's key graphics, the *World Health Chart*, graphs the changes in world health and wealth, as represented by individual countries' life expectancy and GDP per capita, respectively. Compare these two stills of the chart, one from 1958 and one from 2018. The world has clearly advanced in both life expectancy and GDP, as evidenced by the upper-right shift. This change, though, happens so slowly that it goes unnoticed – another major factor in our misunderstanding of the world’s progress.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/worldhealth_1958-2018.png" alt="world improvement - 1958-2018"></center>

<small><small><center>To view full-size image, right-click and select "Open image in new tab."</center></small></small>

Rosling was, in all respects, a brilliant man, but we should not simply take his word that the world has been steadily improving. Instead, let’s put his theory to the test. The goal of this project is to build on Rosling’s analytic approach, while also noting whether we obtain any different conclusions. And there may be some issues with his approach: namely, Rosling only uses one dimension to group countries, and while GDP is clearly important, it may not be all-encompassing when comparing countries to each other. Additional factors, such as those relating to education or health, may differentiate countries better than GDP on its own. Furthermore, Rosling groups countries into four “levels” using differences in country GDP per capita, and as we go from Level 1 to Level 4, we see improvements in countries’ respective lifestyles. Four levels, though, may not be sufficient to compare countries. Choosing a larger number of tiers would allow for more country movement between tiers over time - although choosing too many tiers might result in too much movement, thereby making movement itself meaningless.

Instead of just two dimensions, then, we can collect a larger number of factors, and experiment with more than four country groups with which to compare countries - both to each other and to themselves over time. The [website](https://www.gapminder.org/) for *Gapminder*, a non-profit organization founded by Rosling and his wife, Ola, includes a number of datasets relevant to our analysis. Aside from GDP, we can collect features which are both *representative* of some aspect of a country’s socio-economic status (again, such as education or health) and sufficiently *diverse*. We must also ensure that we choose longitudinal datasets that allow for decade-over-decade comparison, and that inclusion of any one feature does not force us to drop too many important countries from our analysis due to missing values.

Ultimately, we end up with these eleven features, with data collected for each feature in 1990, 2000, and 2010:

*	Cell phone users (% of total)
*	Child mortality
*	Children per woman
*	Food consumption
*	GDP
*	Infant mortality
*	Internet users (% of total)
*	Life expectancy
*	Population growth
*	Urban population (% of total)
*	Years in school

GDP, the one metric Rosling uses to segment countries, likely correlates with other features in our dataset. For instance, a country with more financial resources probably also has a higher percentage of cell phone and Internet users. Another pairing that comes to mind is child mortality and infant mortality, as the two features are very similar in definition. In fact, many of our eleven features are likely correlated with each other; below, we can see exactly which features share correlations, both positive and negative (represented in blue and orange, respectively):

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/correlation_matrix.png" alt="correlation matrix"></center><br>

As expected, many of our features are correlated. Life expectancy and child mortality, for instance, share a highly negative correlation, and child mortality and infant mortality share a positive one. Because so many of our variables are correlated with each other, it makes sense to use principal components analysis to reduce our dimensions. From our eleven features, we can create eleven new features – each feature being some linear combination of the original variables – that explain different percentages of the total variation in our feature set. Then, when grouping and comparing countries, we can focus only on the most essential principal components (i.e., those which explain the largest amount of our data’s variation).

As illustrated below, we can remove all but our first two principal components and still retain nearly eighty percent of our variance:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/principal_components.png" alt="principal components"></center><br>

We can also illustrate our first two principal components on a biplot to examine the features which move together, those that move in opposite directions, and those which are independent of each other (as indicated by features with angles between them of around zero, 180, and 90 degrees, respectively). The x-axis corresponds to the first principal component, and the y-axis corresponds to the second.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/biplot.png" alt="biplot"></center><br>

Our features for years in school, life expectancy, and cell phone users are positively correlated with each other, as evidenced by the small angles between them. This group of features moves at an opposite direction to another set of variables – population growth, children per woman, child mortality, and infant mortality. This, intuitively, makes sense: the first group of features are all beneficial to a country, while the second are all detrimental. The third group, containing GDP, urban population, Internet users, and food consumption, is a bit different; these are the features which distinguish a “good” country (i.e., a country scoring high on all elements of the first group) from a “great” one.

If we show each country’s first two principal components on a scatterplot, we can see where each country falls relative to the groups of variables described above. (Remember that the upper-left direction corresponds with "bad" features, the bottom-left with “good” features, and the upper-right with “great” features.) In doing so, we can also group these countries into “tiers” using K-means clustering. Seven clusters, in this case, seems to make the most sense, as it allows countries to occasionally move between tiers, but not so much that tier movement loses its meaning. Note that only countries’ 1990 metrics are shown below.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/plot_1990.png" alt="country plot 1990"></center><br>

As we move from Tier 7 to Tier 1, we see continuous improvement in countries’ socio-economic wellbeing. In 1990, Tier 1 had an average GDP per capita of over $21,000, while Tier 7’s was only $238. Tier 7’s infant mortality, on the other hand, far surpassed that of Tier 1; an average of 125 infants per 1,000 live births died in Tier 7 countries, compared to an average of only eight children per 1,000 in Tier 1 countries. Further, as we move up to Tier 1, it is clear that the directions of each country correspond to the “bad”, “good”, and “great” feature directions discussed earlier: the lowest-tiered countries are all in the upper-left quadrant of the plot, corresponding with the negative features, while the middle and upper-tiered countries appear in the lower and upper-right quadrants, respectively.

As follows is a more thorough, albeit not comprehensive, list of countries belonging to each tier:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/tier_countries.png" alt="tier countries"></center><br>

So far, we have only observed data as of 1990, and must now also incorporate data from 2000 and 2010 for purposes of country-to-country comparison. It is important to note that when comparing countries over time, all principal components have been “frozen” at the 1990 level. That is, for country metrics in 2000 and 2010, we are not re-calculating principal components, but, for both years, are calculating each metric using the linear weights of the 1990 components. This way, we can make direct comparisons across time, a task which would be impossible had we created a different set of principal components for each of the three decades.

The following alluvial diagram illustrates country movement between tiers from 1990 to 2000 and from 2000 to 2010. The size of each white box represents the number of countries in that tier.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/alluvial.png" alt="alluvial"></center><br>

Many countries do indeed change tiers from decade to decade – some for the better, and others for the worse. Most of this movement takes place within the worst tiers (5-7), as there is only a very small amount of movement in Tiers 1 and 2. In fact, South Korea was the only country at all to move into Tier 1; Italy, on the other hand, dropped out of Tier 1 between 2000 and 2010. Saudi Arabia was perhaps the biggest improver of the bunch, as it was the only country to advance in both 2000 and 2010 (when it moved into Tiers 3 and 2, respectively).

You may also notice that, as represented by its box size, the number of countries in Tier 3 grows significantly between 1990 and 2000. Tier 7, on the other hand, roughly halves during the same time span. This can be attributed to the fact that while many African countries significantly improved in the 90’s (African countries almost exclusively comprised Tier 7 in 1990), a number of other countries were, in essence, “left behind” by their contemporaries. These lagging countries, which include Chad, Niger, and Liberia, were far enough removed from the improved African countries that they warranted their own cluster, which became 2000’s Tier 7. This, in turn, affected the sizes and makeups of Tiers 3 through 6, all notably different in 2000 than in 1990. Between 2000 and 2010, however, the lagging group of countries “caught up” to their contemporaries, which explains why the 1990 and 2010 tiers have virtually identical sizes.

This analysis, though, brings up a crucial question: If a country moves to a worse tier, is the country necessarily *regressing*? For instance, if a country moves from Tier 3 to Tier 4 between 1990 and 2000, did that country get *worse* during that decade?

As shown below, the answer is a clear **no**. The world as a whole is moving farther to the upper-right of the graph – in the direction of our most positive features. This movement is not unique to the best two or three tiers of countries, either; virtually every country in the dataset, regardless of tier, is moving away from the upper left-hand corner of the plot.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/progression.gif" alt="country progression"></center><br>

Here are each tier’s percentage increases from 1990-2010 for three different measures: GDP, years in school, and life expectancy. Note that the lower tiers actually improve, from a percentage standpoint, *more* than the upper tiers:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/pct_improvement.png" alt="country percent improvement"></center><br>

As a further illustration, let’s look at a country that, from 1990 to 2000, *didn’t* improve. This is very uncommon; as shown in the earlier animation, worldwide progress has been uniform among virtually all countries in our dataset. Here, though, is the most prominent example of country regression:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/world-progress/rwanda.png" alt="rwanda"></center><br>

Between 1990 and 2000, Rwanda exhibited the largest decade-to-decade regression of any individual country. While other countries in Tier 6 continued shifting to the right, Rwanda moved to the upper-left of the graph (the exact direction of our worst features). This can be attributed to the 1994 genocide against the Tutsi, which directly impacted Rwanda’s metrics for life expectancy, infant mortality, and child mortality, among other features.

So – what can we conclude about the world? Was Rosling correct to determine that, contrary to popular belief, the world is currently better than ever before? Our analysis certainly supports Rosling’s arguments; even when adding nine features to Rosling’s two, we still see countries improving from decade to decade, regardless of their tier. In future versions of this project, however, I would like to collect data spanning further back than 1990, as well as an even more thorough set of features.

*Special thanks to project teammates [Frawley Barton](https://www.linkedin.com/in/frawley-barton-3507903b/), [Eric Dorata](https://www.linkedin.com/in/ericdorata/), [Anna Kot](https://www.linkedin.com/in/kotanna/), and [Mark McComiskey](https://www.linkedin.com/in/markmccomiskey/).*
