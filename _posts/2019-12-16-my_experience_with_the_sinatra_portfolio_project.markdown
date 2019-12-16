---
layout: post
title:      "My Experience with the Sinatra Portfolio Project"
date:       2019-12-16 22:01:24 +0000
permalink:  my_experience_with_the_sinatra_portfolio_project
---

## Beginnings & Planning
When I began thinking about the Sinatra project, I felt like I had a really solid idea to work on. I decided to make something I actually wanted for myself: an easy way to keep track of everything that happens with Dungeons and Dragons characters. If you don't know what Dungeons and Dragons (or D&D as it's commonly known) is, here is [an explanation from the company that owns D&D](https://dnd.wizards.com/dungeons-and-dragons/what-is-dd).

Essentially I wanted a way make a way track the progress of multiple different characters, with the ability to look back to different sessions of play that character had, and see what happened that session. I particularly wanted to be able to serve a niche of players of D&D, the ones that participate in the organized play campaign run by Wizards of the Coast, called [Adventurers League](https://dndadventurersleague.org/start-here/).

I started early during our Thanksgiving break week, I made a planning document for what models, views and controllers I would need, what attributes the models would need, what relationships between the models I required, and possible ways to expand the project if I finished early. 

I then began laying down a foundation of creating most of the files that I would need, setting up migrations for the models, establishing relationships between the models, etc.

## Implementation

After I got back from the break, I got a slow start to the project, but once I got into the flow of things, I pretty quickly got everything into working order. Having the planning document and the skeleton fleshed out really helped. I had originally planned to follow a test-driven model for the project, but because I got a slow start to the week, I felt more pressure to just get started, and so I just started coding, using my planning document.

I had decided to go with the following model associations:
Users have many Characters, and have many Adventure Logs through Characters
Characters belong to a User, and have many Adventure Logs
Adventure Logs belong to a Character, and have a User through a Character

There were a few points of stress and bugs in this part of the implementation, but for the most part I was able to get it done in fairly quick order. Being able to open up the project in a browser, create an account, and have everything function properly was a really great moment! The barebones of the project was done. But I really only had the ability to keep track of individual adventure logs, and in order to track progress on the character, I had to edit the character itself. I had plenty of time left in the week, so I decided to have the character automatically update when an adventure log was added or changed. 

This was somewhat of a challenge for me, but I decided to go with changing the character attributes to being starting values rather than total values, and added methods to the character model that added the totals from all adventure logs belonging to the character to the starting values. For example, the gold attribute, became starting_gold, and I made a Character#gold method that looked like this:
```
def gold
    self.starting_gold + self.adventure_logs.sum(&:gold_change)
  end
```
It took some time to work out all the changes I needed, particularly with changing all the forms, but it was functioning with a few hours of work. I had a busy weekend ahead of me, but I had plenty of time left in the week, so I decided to implement a new model--Magic Items.

## The Trouble
I wanted to be able to have a character gain a magic item when an adventure log said that they gained a magic item, and then lose an item when an adventure log said they lost the magic item, but to still have a record of everything that happened in each adventure log. I spent a long time just thinking about how the models should work, and I had a very difficult time trying to figure out what the best implementation would be.

Just thinking about it on the surface, I wanted the model relationships for MagicItem to be:
Characters have many MagicItems through AdventureLogs
AdventureLogs have many MagicItems
MagicItems belong to an AdventureLog, and have a Character through an AdventureLog

I just couldn't figure out how the AdventureLog would differentiate between MagicItems it gained for the character in that log, and MagicItems is lost for the character in that log, and I knew I didn't want to make two different models like MagicItemGained and MagicItemLost or something similar, so I was a bit stuck. I felt like I would've known how to implement this in a completely custom defined Object Orientation approach, but I didn't have the level of expertise in ActiveRecord to know how to implement it properly.

I turned to Google and StackOverflow to try and figure it out, and I was unsuccessful for a good period of time, and so with limited time to finish the project, I decided to go down a sub optimal route.

My first attempt at making MagicItem tracking work was to have the only relationship be:
Characters have many Magic Items, and MagicItems belong to a Character.

I then created attributes for AdventureLog of MagicItemsGained and MagicItemsLost. At first I started to look into how to make arrays function in and SQLite3 database, but that felt outside the scope of the project, as well as kind of inherently wrong. I then turned to a really poor solution of simply having these attributes be strings. It was how I had been having the user enter in the magic items gained and lost anyway, and I figured the strings  would just be a record of what happened with the MagicItems in that session. 

I ran into trouble when I realized I would much rather have the Magic Items Lost be checkboxes in the form for AdventureLogs, so that the user didn't have to exactly match the text of a magic item they wanted to lose. It got even more difficult when I got around to reimplimenting the delete and edit routes for AdventureLogs. I was several hours in to trying to make editing route function properly when I decided there had to be a better way of handling this.

## Back to the Drawing Board
I had very limited time in a very busy weekend, but I decided to start over with Magic Items. I figured that worse come to worst, I could just submit the project without magic items, so no harm done.

I decided to try an approach where MagicItems themselves had two more attributes, adventure_log_gained_id and adventure_log_lost_id, which was essentially my own way of hand coding two more specific relationships with the AdventureLog class, but that I didn't know how to do the proper way with ActiveRecord. I then made methods in the AdventureLog and MagicItem models to flesh out the relationship between the two--readers and writers for both sides of the relationship.

At first, this seemed to greatly simplify my routes. I still had to spend a good deal of time refactoring everything, but it all seemed to come together much easier--but then I got to the nitty gritty of very specific bugs emerging with the edit and delete routes. In the end, it seemed like it was a much more elegant solution, though still not a great one, but I realized at the end I probably could've finished it much faster if I had stuck with my original implementation even though it was probably even more sub optimal.

## Wrapping Up
Talking with my course lead in a 1 on 1, she made me realize that I had coded some of my routes in an inefficient manner, as well as a few conventions with which I hadn't 100% complied, so I fixed most of those.

It does function properly now, so I am happy about that. I would like to implement even more features, and perhaps even spruce it up a bit and put it on a live website somewhere, but now getting started on Rails, it seems like a lot of the work I put into this one would've been much better done with another framework. 

All in all, I learned a lot while working on the project, particularly that while I felt like I was saving time on this one by not writing out a bunch of tests in the beginning, it would've been much more efficient in the end if I had written out the tests, as I could've zeroed in on a lot of my issues sooner.

On to the next one!
