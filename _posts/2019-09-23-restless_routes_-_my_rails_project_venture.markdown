---
layout: post
title:      "Restless Routes - My Rails Project Venture"
date:       2019-09-23 15:06:27 +0000
permalink:  restless_routes_-_my_rails_project_venture
---

**FIRST: AN EDITORIAL**

So it' 4:40 AM. I've got work at the animal hospital at 6:30 and it's a 20 minute drive. I like to make coffee and breakfast to wake myself up before I go, but once I'm done there's always another 10 or 15 minutes of undesignated time. This is when I code. 

Well, this isn't the *only* time I code, but in general my programming school fits into very small gaps within the pattern of my life. For this reason, large projects like my Rails portfolio project have to be broken into very small pieces. You fellow coders out there know that this is completely ideal. The abilitity to develope algorithmic solutions to large, complicated problems hinges on one's ability to break those problems into bite-sized chunks. This capability has always come pretty naturally to me. What hasn't come naturally to me is the *social* aspect of the developement community. Need to fix something? Odds are someone already has. Check out StackOverflow or navigate over to someone's github, grab their repo/gem/library/whatever and move on to your next probem! 

It was a long uphill battle, but I finally got on board with the idea of embracing other peoples' code. I could let other people help me with more niche issues while relying on my own approaches to app architecture and the like. And then guess what: Flatiron springs the concept of Restful Routes on me. Not only do I have to trust other coders' solutions for simple, specific problems, now I have to accept entire frameworks based on *other* coders' conventions! 

Naturally, this felt very unnatrul to me. I prefer to build things from scratch. Let me clarify. **In no way am I implying that I am capable of developing the most natural or efficiant solution for ANY, much less every problem.** I simply think, that I - like most people - want to feel exclusively responsible for everything that I've created. A large part of my developer's journey has involved finding ways to come to terms with the reality that this is rarely  the case. Alas I think I've found some shelter in the storm (you guessed it) in the form of a problem. 

As far as I can tell, RESTful routes are not perfect!! Thank goodness for job security, right? 

**THE PROBLEM**

Here's the quick version: A local church that my family is involved with supports an organization based out of Boston that teaches English as a second language to the Muslim community in order to give them an opportunity to seek employment, education and a larger involvement in their community. Currently, the course ciriculum exists entirely in paper workbooks. Naturally, the instructors for these courses have plenty of supplimental material that they want students to have access to, but the best way that they have of sending these materials is through texting. Furthermore, many of these students don't have access to desktop computers, but just about all of them have access to smartphones. 

For my project, I'm making a library for creating, distributing and accessing study materials for all the people involved with these courses. One of my priorites with this application is developing an interface for students with absolutely no english experience so that they can log in and view materials intuitively without relying on an english-based interface. 

**THE PROJECT**

Now we come to the central concern: routing. What is the ideal balance between making an application DRY, and making an interface semantic. I'll tell you where I ended up, but think about what you might've done in my situation? Around 70% of the time I spent building this app involved detail work and building basic functions, but the choices I made when I built my routes and models affected me the entire time. You see, I wanted students *and* teachers to be able to log in and use this same application. They can both log in more or less the same way, but their roles within the app couldn't be more different. So my first inclination was to build two seperate models. They looked something like this: 

```
create_table students do |t| 
  t.string :name 
	t.string :email 
  t.string :password_digest 
  t.integer :course_level
end
```

*and*

```
create_table teachers do |t| 
  t.string :name 
	t.string :email 
  t.string :password_digest 
	t.integer :course_level 
end 
```

Obvious problem, right? First, if I want every user to log in from the same page, I'll need to create 2 forms. I can't use the rails form-builders if I want one form to conditionaly post to two seperate controller actions without putting a lot of unwanted logic in my views. And of course the lack of DRYness is bad enough to make any developer's skin crawl, but what other option is there? Well there's this: 

```
create_table users do |t|  
  t.string :name
	t.string :email 
  t.string :password_digest 
	t.integer :course_level
	t.boolean :is_teacher 
end 
```

Much nicer! Adding the `is_teacher` boolean felt very elegant, but boy were there some hurdles. First off, I want everyone to be able to log in from the same page, right? Perfect, I'll just make a checkbox button that maps to my `:is_teacher` column. That works well enough, but then there's the matter of page presentation in the users' show pages. Suppose I make a helper method `is_teacher?` that I can put in my views to conditionally display certain fields within the page. Already that's pushing it on the amount of logic I want in my views, and then there's the added challenge of styling without lots of bloat in my stylesheet. Man, I just got used to relying on a framework and I'm already about to have to break file-naming conventions in my views! I love the idea of every route looking something like `model_name :resources`, but I doubt if many complex applications can be built without relying on some level of customization in routes.rb. 

Okay, I've accepted that this is a little more complicated than I anticipated, and I'm toying with some potential solutions. The big question for me here was *scope* or *namespace*? I think namespace *could* work if I wanted to make some functions of the site just for a web-administrator, but both the student and teacher parts of this community need to interact with all the same data and models. 

After lots of research and experimentation, here's where I ended up. In my user model I created simple 'student' and 'teacher' scopes that I could reference in my routes. A teacher can create, edit and delete both materials *and* assignments for their students. A student can view materials and assignments, and they can "edit" assignemts in that they can mark them as viewed, but they can't do any altering beyond that. Here is most of what I ended up with in routes.rb. Note that I used a combination of nested routes, custom routes (to student/teacher show and index pages), and scoping techniques.

```
Rails.application.routes.draw do

  resources :courses, only: [:index] do
    resources :materials, only: [:index, :show, :new, :create]
  end

  resources :assignments, only: [:index, :show, :new, :create, :edit, :update]
  resources :materials, only: [:index, :show, :new, :create, :edit, :update]
  resources :users, only: [:index, :show, :new, :create, :edit, :update]

 
  get '/students', to: 'students#index'
  get '/teachers', to: 'teachers#index'
  scope :teachers do
    resources :materials, only: [:new, :create, :edit, :update]
    resources :assignments, only: [:new, :create]
  end
  get '/students/:id', to: 'students#show'
  get '/teachers/:id', to: 'teachers#show'
end
```

That wasn't my only broken convention. If you look carefully above you can intuit that I also had to create a StudentsController, a TeachersController on top of my pre-existing UsersController. Also, I had to add folders for my student/teacher views even though there isn't a student or teacher model anywhere in my application! I've got a lot to learn about best practices, but at the moment I really stand behind my choices here. In my opinion a good coder has a strong commitment to understanding conventions, but is always willing to let go and get creative. 

**A FINAL POINT**

The deeper I get into the development world, the more I believe in coding 'back to front'. Obviously UI is extremely important, but a *beautiful* structure isn't a *good* structure if it doesn't have good bones. Once all the scaffolding was written and stable in my mind, the front-end development side of this project became extremely smooth and extremely fun! How do you think the "bones" look in this application? 

Happy coding everyone! 



Full repo at [github.com/Eli1771/resources_for_ventures](http://)
