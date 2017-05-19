---
layout: post
title:  Metacritic Games CLI project
date:   2017-05-19 02:04:03 +0000
---


I built a command line interface as a final project for the Object Oriented Ruby curriculum in Learn. I chose to use metacritic.com as the source of information, scraping the new release games page for platforms and game, and creating objects from the information gathered.

The project started in a notebook. I wrote out the objects I was planning to create, lists of attributes, and how they would interact. I specced out a basic menu interface, what I wanted to do and look like. 

Programming-wise, I started out following along with the Daily Deal cli code example. Not copy-pasting, but using the basic pattern to set up my gem. I created files for the object classes I wanted to have in the program: Game, Platform, and Genre, and then created the CLI controller class along with the executable to run the whole thing.

I used the CLI controller to initialize two gems I used for formatting: highline for menu creation and input control, and progressbar, which I used to create a progressbar while the cli is loading data from the scraped pages and creating object instances.

I chose to use the #CLI.call method to create the platform and game objects, with the creation of genre objects nested in the game creation logic. This puts all of the loading up front before the first menu prints, and causes a notable load delay, I chose this because I wanted every game instance to be unique by name, with games that have multiple platform releases stored as arrays of platform objects. Everything is pulled in at once, then the cli just reads and iterates through the instances of games and platforms.


Platform and Genre are simple classes. Each instance is initialized with an instance variable, games, set as an empty array. As game objects are created, they are added in to this array, so that each instance of a platform or genre knows what games are associated with it.

Game is the most complicated class in this cli. It is the line that all the other objects hang from. A game can have many platforms, and many genres, which are initialized as empty arrays for each instance. A game will also have unique metascores, user scores, and urls for each platform, which are stored in instance variable hashes with the platform name as the key. This allows the cli to display the scores and link for the game by whichever platform is chosen.

The #self.create_games method creates games from an array of hashes passed in from the cli controller. Each hash in the array has a :platform key, which the method used to determine the platform instance that should be associated with the game. It then pulls the url from the game hash by the same platform and calls the scraper for the individual game page, returning the scraped data in a hash. That data is then checked for content and then assigned as a instance attribute if it is a hash. If the hash value content is returned as an array, the elements in the array are used to create instances of genres, and then assigned to the game. The last step in the method involves changing the hash keys for metascore and user score from the generic :platform to :platform.name.

The Scraper class pulls information from metacritic.com. It also has some regex methods for cleaning up the text that is returned from scraping pages.

I learned a lot during this project, particularly about object collaboration and the importance of using git commit often. There are some things that I'm still not quite clear on, such as when a method should be a class method vs an instance method. Also, reconciling the principles of Don't Repeat Yourself versus Single Responsibility when a method can do two related things. I'm sure when I look back on this code with more experience there will be a lot of choices that I would make differently. For now, I'm pleased that I was able to create this cli by myself with just a bit of assistance from google, stackexchange, ruby-doc.org, and my fellow learners on slack. It is certainly the most code I have ever written by myself without direction, that actually does something in the end.
