---
layout: post
title:      "Jquery rails frontend - Better-Text app"
date:       2017-11-19 23:48:09 +0000
permalink:  jquery_rails_frontend_-_better-text_app
---


For my fourth portfolio project, I replaced some of the functionality of my previous rails project, Better Text, with a jquery frontend using ajax. In this post, I'm going to go through the process of what I did to fulfill the project requirements, and the things I encountered along the way.

Include show resource rendered using jQuery and Active Model Serialization JSON backend:

For the textbook show page, I set a listener for a click event on any link with class "textbook". The listener will prevent the default action, and the fire an ajax request using jQuery for resource that was clicked. Rails will return a JSON response rendered using the Active Model Serializer. The page is then cleared, then that response is used to create a js model Textbook and render an html template using handlebars.js. Once that is complete, the function updates the page url using history.pushState, and then creates a partial within the textbook template for any courses (from the textbook has_many courses relationship) that are provided in the JSON response, and are then appended to the page.

Include index resource using jQuery and Active Model Serialization JSON backend:

Functions for both the textbook index and course index were built for this requirement. Each places a listener for a link with the class "textbooks" or "courses". The listener prevents the default action, and fires an ajax request for the resource. On response, the div is emptied and the data is appened for each resource that is returned.

This was the first function written for this project, and it took some time to find a bug that was only allowing the links to work on the first page load. If I were to navigate away from the root page by clicking another link in the app, then return to the root page, the ajax request would not work any longer. After a few hours, I realized that the document ready function that all the ajax calls are placed in was not working properly due to the turbolinks gem. Because the page was not being fully reloaded, document ready was not working. I changed document(ready) to $(document).on('turbolinks:load"), and everything began working as expected.


Include at least one has_many relationship in information rendered via JSON and appended to the dom:

Modifying the serializers for my 3 models, adoption (belongs_to course, textbook), course (has_many adoptions, textbooks), and textbook (has_many adoptions, courses) to include the acitve record relationships, allowed that information to be returned in JSON responses. A textbook would have a key of courses, with a collection of courses that that textbook had.

Use rails API and a form to create a resource and render the response without a page refresh:

A listener was set up to catch the submit action on the new_textbook form. It prevents the default action, then serializes the form information and does an ajax post request with a JSON dataType. In the textbook controller, the create method will create the textbook and render back the JSON if the validations pass. If the textbook cannot be saved, the controller returns an http status of 422 and a json response with the validation errors.

If the response is successful, the page is cleared, the response is used to create a js model Textbook and render an html template using handlebars.js. Once that is complete, the function updates the page url using history.pushState, and then creates a partial within the textbook template for any courses (from the textbook has_many courses relationship) that are provided in the JSON response, and are then appended to the page.

If the response is unsuccessful, the errors are parsed and then pushed into an alert() message on screen. Once the alert is cleared, the submit button is reset so the errors can be fixed and the form can be resubmitted.










