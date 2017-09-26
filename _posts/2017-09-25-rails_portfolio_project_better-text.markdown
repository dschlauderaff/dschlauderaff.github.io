---
layout: post
title:  "Rails portfolio project: Better-text"
date:   2017-09-26 00:02:45 +0000
---


Better-text, my rails project app, is complete. Or, rather, it meets the project specs for submission. I could probably work on it for weeks to come to add in functionality from the app that I’m drawing inspiration from.

Inspiration is an odd word to put with this project. As I started getting close to project mode in the rails curriculum, I was trying to think of something from my own experience I could use. I didn’t want to make a todo list, or a recipe collection, or any of the other usual boilerplate projects. Then, inspiration struck. I work for a college bookseller. We have an app that is used to manage textbook inventory, and I absolutely hate it. It’s slow, it’s not at all intuitive, and it feels overly complicated for what it needs to do. So naturally, I’m going to rebuild it, but better.

The meat of the app deals with three models. There are models Textbook, Course, and Adoption. A Textbook can have many Adoptions, and have many Courses through Adoptions. A Course can have many Adoptions, and have many Textbooks through Adoptions. An Adoption belongs to a Textbook and a Course, as the join table between the other two models.

Better-text allows for the creation of textbooks alone, courses alone, or creation of a textbook, course, and adoption at the same time through nested forms, resources, and attributes. The app also allows for adoptions to be created from existing courses and textbooks. Textbooks are created with a title, a price, and a default inventory of 0, courses have a name and an enrollment, and adoptions are created with a fulfilled boolean that defaults to false upon creation.

Every textbook with an adoption can be ordered, which increases that textbook’s inventory by the enrollment number of the course it’s adopted to, and sets the adoption’s fulfilled status to true. This allows for a reports menu that can search for any unfulfilled adoptions and show them to the user.

The User model was created using the Devise gem, with omniauth using Facebook for a secondary sign in strategy. Anyone is allowed to view indexes and show pages, but all other actions are restricted to signed in users. I wanted this functionality so that anyone could use the app to see what books are adopted, inventory levels, courses available, but changes could only be made by someone with an account. Employees can see what’s going on, but managers need to be the ones creating and ordering.

I implemented bootstrap to the layout and forms through the use of rails bootstrap gems. This took some time to get running, and was not necessarily required, but I want the project to look nice. The experience of problem solving the implementation was helpful.

The requirements for this project did a great job of pulling together everything covered in the rails curriculum, and I really feel like my rails skills were refreshed and sharpened by it. In particular, I spent a lot of time researching nested forms and resources, using activerecord, and routing.

The time spent working on this app actually made me appreciate the app I use at work a bit more. It’s still ugly and slow and complicated, but it clearly took a lot of effort and time to build in all the functionality that it contains.

I deployed my app to heroku, using basic instructions found with google. The process was mostly painless, but I’ll be writing it up separately as a guide for future use.

