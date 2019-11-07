---
layout: post
title:      "My Experience with the CLI Data Gem Portfolio Project"
date:       2019-11-07 15:30:18 -0500
permalink:  my_experience_with_the_cli_data_gem_portfolio_project
---


**Beginnings**

I started off with two possible ideas for my project: a scraper for Magic: the Gathering decks or a scraper for top charity organizations. Both are interests of mine, and I thought both could have interesting object oriented designs to them. Magic has decks that have many cards, and cards that have many decks, whereas charities have many donors, and donors have many charities.

I perused the web for good sources of information on both topics, and quickly found that the official Magic website would be very difficult to use for this project, and that while my favorite charity ranking website CharityNavigator did have good access to charity data and construction of their HTML for scraping, it didn't easily have donor information available.

I knew of a different community made Magic: the Gathering website that I liked, called mtggoldfish.com, so I decided to check that one out. At first glance it seemed excellent for my purposes, and digging around the HTML, it seemed fairly scrapable, so I decided to use mtggoldfish.

**Process**

*-Planning*
My first step was creating a Google doc where I defined the classes I thought I was going to need, and the essentials of what I needed each class to be able to do. I referred back to this document constantly through development so that I did not lose sight of the big picture. I would definitely use this approach again, though the specific methodology I would change, as I used a Google Spreadsheet for this, and that was not ideal.

When I started coding, I started with the OO side of the program. I knew I wanted it to be at least somewhat test driven, so I familiarized myself a bit with how RSpec works, so that I would be able to write tests that actually worked properly. I mostly perused old labs to see how it worked, though I also checked out some rspec tutorials online. 

*-Object Oriented Design*
I mostly went class by class, designing a Deck and then a Card class through my tests and then implementing what I needed to in the class. I would occasionally come up with a new idea while I was programming the class for some functionality I might need, and while I was not always diligent about designing the test first before implementing it, I was constantly using rspec to check my work, which was very helpful.

This section of the project took me a lot longer than I thought it would, because I thought I had a really good handle on OO programming in general, but it may have just been because I was used to the guidance of the labs.

*-Scraping Data*
I then moved on to the Scraper class, which I also interweaved design and testing into. The testing was a bit difficult, because I didn't really want to get into the complications of using RSpec to capture method calls and returns, so I would write the tests to test my outputs as I was able, then I would refactor the tests as I got better and better at giving the output I was looking to give in the final iteration of the code. 

I found the actual scraping part of the program to be mostly straightforward, though there were certainly a few sticking points--I realized a lot of the information was actually repeated in separate tabs on the website, so I had to narrow my css selectors, etc. I never did figure out how to implement one particular thing that I wanted--in order to separate out cards from sideboards, the only indicator on the website was text inside and h2 with no class or ID, so I was unable in limited time to sort that properly.

*-Command Line Interface*
When I got to the CLI section of the project, I mostly abandoned the test driven approach, as I found it easier to simply test that with the CLI itself and binding.pry statements. At several points during the CLI section I realized I either left something out of the original design of the OO side of the project, or that I wanted to add in some new functionality. The biggest change I had to make was that I realized with the way I decided to implement the project, I was going to need a new class--the MtgFormat class. 

I originally designed the program to just scrape the most popular Modern decks, but I realized the other formats were basically identical in structure, so it would be silly not to include them as well. What I didn't realize when I decided to do that, was that I was going to have to refactor a lot of how the OO side worked. It didn't end up being to bad, and I ended up doing a Formats have many Decks, Decks have many Cards, Cards have many Decks, Formats have many Cards through Decks, and Cards have many Formats through Decks model. This doesn't exactly reflect real life, but I wasn't going to implement and exhaustive card database, so it was quite functional for the application. 

I then changed the Format class to be MtgFormat to avoid confusion with the format method.

There were a lot of finnicky changes that I had to make during the CLI portion which ended up being time consuming, but I essentially finished with the project fairly early, so I decided to make the presentation a bit nicer by implementing the colorizer gem. That was a lot of fun. It was a bit confusing at first to make it work, but it ended up being pretty cool to be able to have the cards be the color on the screen that they are in the game.


**Finishing Up**

Unfortunately, soon after finishing the bulk of the programming for the project, I fell quite ill with the flu, which severely delayed me putting the finishing touches on the project--writing the README, this blog, making my Walkthrough video, etc. That was very frustating, in addition to the misery of being very sick. But hey! It all came together in the end!

Thanks for reading!
