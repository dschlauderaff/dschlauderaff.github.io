---
layout: post
title:  "Sinatra Project Toy Tracker"
date:   2017-06-28 12:29:25 +0000
---


This project started originally to track stuffed animals belonging to users. After working on it for a bit, I realized it's actually a program to associate any toy with an owner. As a father of three boys, I can see a need to be able to definitively establish what toys belong to which child. The program is written in a way that all owners can be viewed, as well as all toys, but only the owner can make changes to a toy, including assigning it to a new owner. Useful for a parent account to create toys, then assign them to a child's account. This will also allow for the children to give toys to each other.

Once I had a direction for the project, I found the execution of the sinatra app to be easier than that of the cli gem. The use of Active Record and Sinatra to abstract the object classes and their associations meant I could write the project in a long weekend instead of the two weeks it took to create a cli gem from base ruby. Following the patterns established in the labs leading up to the project made for a simple step-by-step where the only changes required where for the attributes of the objects themselves.

First, I created migrations for the two models: a owners table and a toys table. I wanted the toy class to have a name and a type of toy, but after I wrote the migration, I discovered that :type is reserved in Active Record, and had to change the attribute name to :variety. I had a few more stumbling blocks creating the data base, changing column names, but forgetting to change the association macro name in the model itself, all easy fixes compared to chasing down bugs in the cli gem project.

For the controllers, I set the main application controller to handle the main index that links to account creation and login routes, along with /success and /failure routes that I used for testing as I built out features. The main controller also has three helper methods, #logged_in? - checks for user.id in the session hash, #current_user - returns the Owner based on the user.id in the session hash, and #user_log_in - authenticates username and password and then redirects to the success route initially, and later changed to the owner's index page.

In the owners controller, I set up routes to handle account signup, account login, an index of all Owners, and an indvidual owner show page that would show all of that owner's associated toys. The toys controller was set up to handle new to creation, show page for individual toys, an index of all toys, attribute editing and toy deletion. The routes are built so that only the toy's owner is allowed to update attributes and delete a toy, though the toy itself can be seen by any of the users of the system.

For views, I created a layout.erb file to be the base page of the app, containing contextual nav links to owner and toy indexes, login and account creation, and the owner's home index. Toys have a create, index, show, and edit view, while owners have create, index, and show views. There is also a main index page that contains links to acount signup and account login.

Updating and deleting toys is done by way of the toy show page. If the session user.id matches the toy's owner.id, two buttons appear on the show page for editing and deleting. Even if the route is typed in the browser, it will redirect to the index if the user.id is not the toy owner's id.

Styling the app was a bit of a challenge for me. I did not want to spend an excessive amount of time on it, but I didn't want to have a bare app, either. I found a resource on github for simple styling for sinatra apps: https://github.com/webdevjeffus/css-for-sinatra. I added the drop-in.css to the /public/css folder which added some nice, simple styling to the app.

