---
title: "Rap Network Analysis: Which Rappers Work Together?"
date: 2019-04-09
permalink: /projects/rap-networks.html
excerpt: "Because I just can’t stay away from rap-related projects."

---
<hr>
I’ve written my fair share of rap-related posts on this website. Today, though, I’ll be focusing on song titles, not lyrics. Using the [*geniusr*](https://cran.r-project.org/web/packages/geniusr/index.html) *R* library, I scraped thousands of song titles from Genius and collected a list of all rapper **features**.

The A$AP Rocky song [*1Train*](https://www.youtube.com/watch?v=nU4OIAYwo5g), for instance, features six different rappers: Kendrick Lamar, Action Bronson, Joey Bada$$, Yelawolf, Danny Brown, and Big K.R.I.T. Six different items, then – one for each featured artist – are added to the features list, with each one noting A$AP Rocky as the original song artist. (Imagine this process repeated nearly 70,000 times.)

Now, using the Python [*networkx*](https://networkx.github.io/) package, we can look at an entire mapping of which rappers work most closely together.

First, though, some important notes about the project:

* The relative locations of every artist are *approximate* and should not be taken literally.
* An artist must have at least 25 scraped song titles for any connections involving that artist to appear.
* The resulting map makes no distinction between rappers who *featured* on the song, and rappers “*credited*” for the song. (That is, J. Cole appearing on a 21 Savage song is treated the same as 21 Savage featuring on a J. Cole song.)
* No connection is made between two artists who featured on the same song, if neither were credited for the song.
* Songs must be credited to only one artist on Genius. For instance, the song [*Sunflower*](https://genius.com/Post-malone-and-swae-lee-sunflower-lyrics) is credited to the artist “Post Malone & Swae Lee”, rather than just one of the two artists.

Let’s take a look at the resulting map. Because the image is so large, you’ll likely have to right-click the image and view it in a new tab.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/rap-networks/networks_1x.png" alt="rap networks"></center><br>

The network map does an excellent job of visualizing different “groups” of rappers who most commonly feature on each other’s songs. For example, the members of Odd Future, Pro Era, and the Wu-Tang Clan, among other groups or collectives, appear to have their own distinct respective “sections” of the map.

We can even see different styles – or even *eras* – of rap appear in different areas on the network. Much of the left-hand section of the map is dedicated to rap from the 1990s or early 2000s, whereas many “SoundCloud rappers” appear farther towards the right. In addition, nearly all the most “mainstream” rappers appear towards the center of the map, which makes sense; artists like DJ Khaled, Rick Ross, and Lil Wayne, by virtue of their popularity and longevity, have had the chance to work with a particularly widespread set of rappers.

All data and code are posted on my [*GitHub*](https://github.com/saisenberg/rap-networks). Hope you enjoyed!
