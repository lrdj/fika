


User experience
===============

There is a text file. This file contains the names of people. Note the number of people.

A browser page is opened. Nearer the top of the screen (ie in the sky) it shows a number of friendly-looking bumblebees. The number of bees displayed is the same as the number of people in the people file. Next to each bee is a label, clearly showing a person's name. Each bee is assigned a name from the list in the text file.

At the bottom of the screen is the grass. 
In the grass are flowers
The number of flowers is the same as the number of people in the text file.
The most central flower is 10% taller than the other flowers and is labelled "Fika"

In the centre of the screen is text that reads "Who is hosting Fika next week?" and below that header is a button labelled "launch the bees"

When the user clicks on the button, the bees start to buzz around the screen, hovering randomly above the flowers for 2 secs.

Then, after 2 seconds, the bees make their way down to the bottom of the screen and settle down on the flowers, one bee per flower.

The bee that landed on the fika flower is the person who is hosting fika next week.


Stack
=====

- This is a fully static site (Jekyll) hosted on github pages
- The script and html + css is in one file
- The data is in another file (json or yaml of whatever works best)
- There are no imported assets from outside of the repo. If you want to use a fancy font or images, we need to import them to the repo
- Sound would be nice (buzzing sound for when they hover, and a nice 'ahhh' sound for when they land)

Overall this is supposed to be simple and robust. It should work on a phone as well. Stylisitically it doesn't have to be fancy, but the look and feel should be bright, light-hearted and fun. This tool is used by the delivery manager as part of a ritual so it should make folk smile when the bees start moving.



