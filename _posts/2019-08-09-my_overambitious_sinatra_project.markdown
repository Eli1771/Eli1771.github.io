---
layout: post
title:      "My Overambitious Sinatra Project"
date:       2019-08-09 19:00:57 +0000
permalink:  my_overambitious_sinatra_project
---


Woopsa-daisy! I think I'm finally done, but sheesh that was tiring. 

So I just finished my Sinatra project (I think). Obviously, the application I made is way outside of the project requirements, but I'm happy I went for it! 

THE PREMISE

So when I started Flatiron, I wanted all my portfolio projects to be projects with meaning. Right off the bat I told those closest to me to brainstorm some application concepts for met to use to make my unit applications. My lovely stepmom had an idea for right off the bat. She works as a pasture's assistant for her home church, and they have a library. The library consists of multiple rooms, each with multiple bookcases, each with many books. How do you organize such a vast collection? Why, with a spreadsheet, of course! 

Multiple people over the years have had her position, so the spreadsheet for the library has gone through many incarnations, each with different naming, styling, and organizational conventions. Now she has this big, ugly, thousands-of-entries long list of inconsistently formatted rows describing books in ways that all make different amounts of sense to different people. If you've ever worked in administration you know that it isn't weird to work with documents and resources that have been around far longer than you or your position. Her basic request was: fix this. 

THE APPROACH 

The example project that Flatiron gives is a web-app for a golf club collection. That's awesome! I don't want to dis anyone who endeavored to make a simple, two-modeled, one-object-listing CRUD app. That being said, I like the idea of making things that can be built up to be used in the real world eventually. 

The first think is the models. I went through a lot of versions of table-layouts and model relations before I landed on the current (submitted) version. The book object has two aspects. There are object that relate to info about the book (author or authors and topic), and there are objects that relate to the book's location. This second object creates a sort of Russian nesting-doll effect. A user has a library, which has rooms, which has bookcases, which has shelves, which has books. All of these things ultimately belong to the user through any number of connections, but the complexity of these model relations created a lot of problems for me.  Obviously there are many different ways to skin a cat here. I went with a very familiar two-controller MVC. I do want to say that I could have easily created a separate controller for each object type, but I wanted to make a book-centric UI, so I'm happy with my choice to use a controller just for users and libraries. 

The library controller directly interacts with all objects (except the user object), but it uses the book object as a center point. The biggest hurdle that I had to overcome was finding a way to make all of a user's content exclusive to that user. To display a collection of objects in a view, you first have to make those objects available to the view in your controller. I had to grab only those objects that belonged to each user in order to make sure that my library maps stayed 100% accurate. This made it necessary to use nested enumerable and long attribute change to grab collections or objects related to one user:

```
@user_bookcases = Case.all.select {|bookcase| bookcase.room.user == current_user}
```

Or in the cases of grabbing all the Authors who have written books in a User's collection: 

```
@authors = Author.all.select {|author| author.books.any? {|book| book.user == current_user}}
```

Whew! I know these are pretty big, ugly lines of code, but they work for what I'm trying to do. The biggest issue with my model structure right now is that there isn't much room to grow. You can edit books, and their included attributes, which includes location attributes like rooms and bookcases as well as the descriptive attributes like authors and topics. Unfortunately, right now there's no way to - for example - edit the number of shelves on an existing bookcase, change the spelling of the name of an existing Author, or delete a room from the map of library. 

(My plan is to submit the application now that it meets the project requirements and add more functionality to it later. It's already a bit overcomplicated as it is, and I'd hate to overstretch myself and break it right before it's time to submit.)

In any case, when it's time to add editing capabilities to the rest of the objects my library controller will start getting pretty overcrowded fast. I'm already stretching the bounds of the rule of separation-of-concerns pretty thin by cramming the initialization of four different objects into one form and one controller action. I would love to stick four forms for initialization onto one '/lib/new' page with four submit buttons and four controller actions for creating each of the object types. You could still dynamically generate each of the forms by creating checkboxes and radio buttons for exiting objects, but things would look a whole lot cleaner with out ten different text-boxes in one form. 

I think my favorite part of the application is the UI for each of the show pages. Normally that is *not* my jam, but I can see how people get into it. I love the russion-nesting-doll visuals and that each div is a clickable link even if it just looks like it exists for visuals. I'm interested to see if other people find it as intuitive as I'm expecting, because I know that these links don't *look* like buttons, and that can be a deal breaker for a lot of people. 

I'm very interested to hear what other people have to think, so feel free to check out the repo here: https://github.com/Eli1771/personal_books.git

In any case, I'm excited about how approachable this was! There is some room for some front-end JS in the future, and I look forward to making some more logic-heavy applications using the MVC framework in the future. I hope everyone enjoyed Sinatra as much as I did, I can't wait to finally learn Rails! 

Happy coding! 

