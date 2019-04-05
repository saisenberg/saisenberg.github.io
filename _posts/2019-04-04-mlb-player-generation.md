---
title: "These Baseball Players Do Not Exist"
date: 2019-04-24
permalink: /projects/mlb-player-generation.html
excerpt: "Q: What's easier than organizing an expansion draft? A: Generating your own roster of fake baseball players."

---
<hr>
Most of my projects thus far have been focused mainly on prediction - especially involving professional sports. Lately, though, I’ve tried to place a heavier emphasis on content *generation*. The most notable project in this category to date has been the [*SongwriterBot*](https://saisenberg.com/projects/songwriterbot.html), which uses Markov chains to randomly generate its own lyrics. This post, though, will take more of a deep learning approach to content generation, and rather than generating sequences of words, we’ll be generating images of non-existent – but (hopefully) realistic! – baseball players.
<hr>
Up front, I should note that I learned the majority of code for this project from an excellent guide on [*Medium*](https://medium.com/datadriveninvestor/generating-human-faces-with-keras-3ccd54c17f16). As such, I won’t be posting my code on GitHub, but would instead recommend that everyone interested in replicating this sort of project check out the above link.

In order to generate pictures, we’ll be using a deep learning architecture called Generative Adversarial Networks, or <i>GAN</i>s. Popularized by a 2014 Ian Goodfellow [paper](https://arxiv.org/abs/1406.2661), GANs are comprised of a pair of neural networks which “compete” against one another. The first model, a *generator*, creates series of fake images in attempts to confuse the *discriminator* model into being unable to distinguish real images from artificially generated ones. Data scientists have used this architecture to create some [incredible-looking images](https://thispersondoesnotexist.com/) – so incredible, in fact, that even *humans* can’t always tell the difference between real and fake pictures. My artificially created baseball players probably won’t reach quite that level of intricacy, but we’ll hopefully be able to create some realistic images.

Before we generate anything fake, let’s first look through our dataset of real images. I wrote a small Python scraper to iterate through individual player pages on [*baseball-reference*](https://www.baseball-reference.com/), saving each player image to my desktop as we go. For the sake of time and convenience, I’ve only collected images of players who made their major league debuts during or after the 2000 season. Because most images involve a relatively similar “setup” (i.e. the ballplayer smiling, directly facing the camera, and wearing his cap and uniform), our generator should theoretically be able to “learn” and recreate this setup relatively quickly. Whether it’ll be able to trick the discriminator (and, in turn, humans) into believing its generations... that's another story.

It’s important to note that the generator – at least how I’ve set it up – expects every image in our dataset to be equally sized. After scraping each image, then, I use the [*PIL*](https://pillow.readthedocs.io/en/stable/) library to reshape every image into a standard size. For reference, here are a few examples of the images our model hopes to replicate; you’ll see that they’re all equally sized and adhere to the setup described above. Feel free to reach out if you’re interested in the scraper code or the pictures themselves.

<center><img src="{{ site.url }}{{ site.baseurl }}/images/mlb-player-generation/resized_scraped_images_sample.png" alt="resized scraped mlb players"></center><br>

Our generator will accept a series of a hundred random numbers as an input, and will use a series of convolutional layers (along with upsampling, batch normalization, and dropout at each layer) to generate an array of size 64x64x3 as an output. Each of our output images will be 64 pixels wide and 64 pixels high, and, because we’re working with colorful images (in an RGB scale), we use 3 as our third dimension. Every number in our resulting array corresponds with an RGB value, which can range between zero and 255. Note that if we were generating our images in grayscale, our output would be in a 64x64x1 array, rather than 64x64x3.

The input to our discriminator model is a 64x64x3 array, which represents an entire player image (which can be fake or real). The components of the image go through another series of convolutional layers, but the output of the discriminator is only *one* number, ranging between zero and one. The closer the output is to one, the more confident the discriminator is that it’s identified a real image, rather than an image created by our generator.

Once we set a certain number of epochs and a batch size, we can keep training our generator to – at least in theory – create increasingly realistic images. During every epoch, we'll train our discriminator on a number of fake images (with this number being equal to our batch size), and an identical number of real images, which we respectively label as *fake* and *real*. Then, we generate a new set of fake images, but try to trick the discriminator into mislabeling our generated images as real. After every hundred epochs, we’ll save a set of fake images to the desktop so that we can observe the generator’s learning progress over time.

Let’s see how our generated images change over the first thousand epochs, starting with the very first set:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/mlb-player-generation/gif_0-1000.gif" alt="first thousand epochs"></center><br>

After epoch #1, our generator essentially knows nothing at all – its output is entirely static. By the hundredth epoch, the model has already learned enough to create a very fuzzy set of head-shaped objects, which become progressively more well-defined as we iterate through the epochs. By epoch #1000, the images are still fuzzy, but the generator has clearly learned much more about what “constitutes” a face.

Let’s see how the generator improves through the twelve thousandth epoch. We’ll keep observing changes in hundred-epoch intervals, but we’ll move a bit faster now:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/mlb-player-generation/gif_1000-12000.gif" alt="epochs 1K to 12K"></center><br>

It’s a bit harder to follow the generator progress through so many images, but we can see the model creating more and more detailed pictures. Actual facial features, facial hair, and even ballcap logos begin to appear in our generated images, and the images have become sharper and more well-defined.

Finally, we’ll see how the generated images change up to the sixteen thousandth epoch (which is as long as I've run the model so far):

<center><img src="{{ site.url }}{{ site.baseurl }}/images/mlb-player-generation/gif_12000-16000.gif" alt="epochs 12K to 16K"></center><br>

There’s clearly still more work to be done, but we’ve generated images that legitimately resemble real baseball players! While these pictures might not fool anyone as they are (I should mention that by the 16,000th epoch, the discriminator was still able to distinguish real from fake), re-scaling the pictures to their original aspect ratios might make the task slightly more challenging. Just for fun, I’ve compiled some of the best-looking generations below:

<center><img src="{{ site.url }}{{ site.baseurl }}/images/mlb-player-generation/best_generated_images.png" alt="best generated players"></center><br>

By continuing to fine-tune the model’s parameters, as well as running the model for more epochs, I hope to eventually create generated images that are truly indistinguishable from real ones. Parameters to tune include learning rate, number of convolutional layers, and number of nodes at each layer, among many others. Again, many thanks to the [AI Insider](https://medium.com/datadriveninvestor/generating-human-faces-with-keras-3ccd54c17f16), whose article was instrumental in preparing this project. Hope you found it interesting!
