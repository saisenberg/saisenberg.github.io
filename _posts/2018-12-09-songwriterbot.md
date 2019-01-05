---
title: "Lyrics, Pt. 2: Automated Songwriting"
date: 2018-11-03
permalink: /projects/songwriterbot.html
excerpt: "For all aspiring musicians out there - SongwriterBot's here to make your job a little bit easier."

---
<hr>
For quick access to *SongwriterBot*, click [here](http://songwriterbot.herokuapp.com/)!
<hr>
In [Part 1](https://saisenberg.com/projects/lyrics-classifier.html) of my lyrics project, I scraped thousands of song lyrics from [*Genius*](http://genius.com/) and developed a model which classifies songs into genres based solely on their lyrical content. Now, in the second and final installment of the project, I’ll provide a brief explanation of how I developed *SongwriterBot*, which writes its own randomly generated songs.

This project utilizes Jeremy Singer-Vine's amazing [*markovify*](https://github.com/jsvine/markovify) package to create separate Markov models from the scraped songs of each genre. If you read Part 1, you’ll know that I scraped 1,500 songs per genre; here, I scraped approximately ten thousand country, metal, pop, rap, rock, and soul songs. For each genre, every song was split line-by-line and combined into a Markov model, which can randomly generate sentences from the text.

The following picture does a good job of demonstrating how the Markov models generate sentences. In this example, the Markov model includes just two sentences: “Mary had a little lamb” and “Mary had a giant crab.”

<center><img src="{{ site.url }}{{ site.baseurl }}/images/songwriterbot/markov_pic.png" alt="Markov explanation"></center>

At the beginning of the sentence, the model only knows one possible word: *Mary*. Directly after *Mary*, the model has only seen one word: *had*. After *had* comes only *a*. After *a*, though, the model has seen two possibilities, each an equal number of times: *little* and *giant*. It randomly picks one of the two options, and continues as normal. It is this exact process – albeit, with far more possibilities – that occurs hundreds of times during the creation of one *SongwriterBot* song.

While back-to-back lines aren’t always thematically consistent, the content of the lines themselves tends to imitate each genre very well. Here is an example stanza for each genre:

**Country**: but i don't understand / it would take my hand / and i won't let me get my wheels / the screaming wheels and blackjack deals

**Metal**: look at the seventh veil / too blind to see the white whale / we're on our crooked scale / divine - night in jail

**Pop**: you hate the word and we can come on and dance / don't want to see the private jets to france / he's giving me the vodka skip the criss / we are what they say that you would never miss

**Rap**: i smoke on some g s**t / international bring back piper's pit / i scratch off on they dream / so when i'm with that guillotine killer team

**Soul**: don't take your love from the start / love will never be apart / whenever you want to stick / that we have come here quick

Even better, we can also create “genres” that don’t actually exist. I added the entirety of the *Harry Potter* and *A Song of Ice and Fire* (more commonly known as *Game of Thrones*) book series as separate Markov models. *markovify* also allows users to combine multiple models – here are a couple amusing stanzas of a Harry Potter & rap song:

**Harry Potter / Rap**: i shouldn't say s**t and they fall / the room at the yule ball / i let you have your mother's eyes / but we've got to be cooked in pies / you give them to the ministry instead / grab the chrome to ya head / any form of cedric stood there / i walk in the cliff-top garden and into the air

If you’re interested in generating your own songs with *SongwriterBot*, click [here](http://songwriterbot.herokuapp.com). Because Heroku limits free web applications to 500MB, I removed rock, *Harry Potter*, and *A Song of Ice and Fire models* from the available options, as well as the ability to combine genres. Note that the app still occasionally runs into memory bugs and timeouts, which I am working to fix.

Once you generate a song, you can save it – which I recommend, seeing as the program will never create the same song twice – or try out your new lyrics against an instrumental track for any genre. Hope you enjoy!
