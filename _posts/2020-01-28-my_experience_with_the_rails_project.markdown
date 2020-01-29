---
layout: post
title:      "My Experience with the Rails Project"
date:       2020-01-28 19:37:45 -0500
permalink:  my_experience_with_the_rails_project
---

## A Slow Start

I began the Rails project with a number of stops and starts--I had several ideas that I began coding, that I realized wouldn't actually fit the specifications of the project's requirements--specifically the required model associations. 

My first idea was a project that would help keep track of which shows a user watches and which streaming service they are on. The idea was a user has and belongs to many shows through streaming services, which would satisfy the requirement. But then I realized that I was also required to have at least one belongs to relationship, and the streaming service would make more sense in a has many relationship with both users and shows, and so I abandoned the idea.

Then I had several ideas revolving around a site that would assist Role Playing Game Masters keep track of their non-player characters, or encounters, or several others, but I kept realizing part of the way through that they all shared a similar problem, in that the models wouldn't make that much sense in a many-to-many relationship.

I finally struck upon the idea of doing a board game review website, where a user has a many-to-many relationship with board games, through reviews. This seemed perfect. 

## Getting Everything Set Up
I started by setting up the rails infrastructure, putting some basic models together and setting up a few relationships. I came upon the idea of adding all the designers, publishers, and artists of different board games as models, so that users would be able to find board games by looking at other games by creators they liked. I also added in a category model for the same reason. These decisions would come back to haunt me, as it made my project much, much more complicated.

Having gotten some of the preliminaries out of the way, I remembered that my course lead had advised me in getting Devise and Omniauth set up first, as it could be a bit finnicky, so I worked on that. 

I couldn't get Omniauth and Devise to work together properly for some time, and I realized that it was because I had done set up for both Devise, and Omniauth seperately, as if I was setting up Omniauth without using Devise, and so I was setting off a flag by asking for Omniauth info twice. Once I realized my mistake there, I quickly got Omniauth working with Devise with GitHub.

I briefly attempted to also have Omniauth working with an Amazon account, as it made more sense to me since Amazon is a great place to buy board games, but it was more complicated, and required an actual live URL for the site, so I abandoned that idea.

Even though it gave me some issues, Devise is awesome! Even with the time loss for figuring out strict settings, it definitely saved me more time everywhere else.

## Inheritance in Rails
Because I added designers, publishers, and artists as models to my project, I wanted to examine inheritance when it came to Rails. I knew that all three of these models would just have a name field, so it made sense to me to explore inheritance, since all of their functionality would essentially be identical. 

As I researched the best way to implement inheritance in rails, I couldn't find anything that exactly matched the associations I was looking to use with these models. All 3 have a many-to-many relationship with board games, and I couldn't find an example of inheritance with a many-to-many relationship. At first, I found an article about implementing multi-table inheritance that I thought would work fairly well, but then I saw multiple other sources saying to never do that, so I figured it was best I set that aside. That left single table inheritance and polymorphic inheritance. Since all the models were nearly identical, it seemed that single table inheritance made more sense, so that's what I chose.

I tried for a long time to be able to make one join table for the has-many-through relationship for all three different models and board games, using the parent class for designers, publishers, and artists. I thought it would be the most elegant way, but I just couldn't seem to get it to work effectively without also implementing a polymorphic inhertiance, which seemed to defeat the purpose. So I eventually succumbed and created a join table for each of the three models and board games.

## Realizing My Mistake
Having gotten most of the work for the models and associations squared away, I began to work on my routing and controllers in earnest. An hour or so into working on this I realized what I had done by including the extra models--I would have to create routes, controllers, and views for all of these things in addition to all the work I was doing for users, reviews, and board games. I briefly considered abandoning them to save myself the time and work, but I let myself give in to a bit of [sunk cost fallacy](https://www.behavioraleconomics.com/resources/mini-encyclopedia-of-be/sunk-cost-fallacy/), since I had spent all that time trying to figure out how to make the inheritance work.

I had been planning on creating rspec tests for everything in my application, especially because of how helpful I realized they would be after my last project, but the thought of having to make all those extra tests put me off the idea. It ended up not being nearly as bad as last project, as I was heavily relying on Rails and Devise to carry the load, and I had a pretty good idea of what I needed to accomplish and how to do it.

## Getting a Move On

At this point, I was already quite far into the project week, and so I needed to get a lot done in a short amount of time. It was mostly just a matter of typing up routes, controllers, and views. There were a few different moments where I had to think deeply or look up about how to implement partials in a certain way, or how nested routing worked with forms, especially creating new objects.

## Wrapping Up

Because of the slow start and the amount of extra work I gave myself, I ended up finishing work on the project in the following week. I waited to implement validations, scope methods, and error flagging on forms until the end, which all went relatively smoothly, though it did take me some time to figure out which methods would be useful as scope methods.

At the end I did spend some time fixing/changing my implementation of how I calculated average ratings for board games. I almost decided to try and implement a Bayesian algorithm for calculating average rating, but decided that was better left for another time when I glanced over dense mathematical articles on the topic. 

I would have liked to spend more time on making BetterBoardGaming look a bit more presentable and functional, but I had already used up too much time.

It was a valuable project though, I learned a lot, and got much more comfortable living in my code and navigating through all the different routes, controllers and views. So glad I finished it!

Find the repo [here](https://github.com/jesselsmith/RailsProject/)!
