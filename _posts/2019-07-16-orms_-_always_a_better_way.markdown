---
layout: post
title:      "ORMs - Always a better way "
date:       2019-07-17 02:00:13 +0000
permalink:  orms_-_always_a_better_way
---

*"Do something, even if it's wrong" - My Dad 

I call it perfectionism. Really though, that's just a way of protecting my self-esteem. When you get a great idea, start building it out, and then promptly freeze up every time you hit even minor roadblocks, it's called procrastination. You could also argue that it's called fear-of-failure or just plain old laziness, but in any case, I **definitely** can't call it perfectionism.

If you've ever built a web-app from scratch, you know that it probably takes more than some rudimentary front-end knowledge and some jQuery. When it comes time to start storing data, working with sessions, and interacting with a server it can be hard to know where to begin, especially if you're self-taught. There are countless directions to go, and each convention comes with it's own set of rules, keywords, and best practices. In other words, it's a commitment. You can always go back and learn more. If you try to find the perfect syntax, library, or tool every time you approach something new you'll end up where I always did: stuck writing huge, cumbersome methods with procedural JS in order to solve problems that have **already been solved a hundred times before.** It can be nerve-wracking, but it's always better to try something new than to reinvent the wheel over and over again. 

I re-learned that little life lesson recently when I was introduced to the ORM concept. I love building logic, and I love the puzzle of of mapping out database, but I find working between the two to be an entirely charmless excercise. I'm sorry if you love it, but I've never seen the appeal. I was delighted, then, to find that Active Record is a Ruby-specific library of methods that turn Ruby objects in tables and relate them for you in a very sleek and intuitive way. This frees up developers to think in object-oriented - or real world - terms and leave the data management aspects up to the ActRec tools. Of course, you can still manually tweak your tables to suit any odd needs you might have. And you can use a mixture of ActiveRecord and hard-coded SQL queries (still written in your .rb files) depending on your program requirements. 

Perhaps the thing I love most about the Ruby community is their commitment to heavily standardized - if not strictly official - naming and formatting conventions. For example, converting the ruby object LocalLibrary to the table local_libraries is both easy to remember and logically semantic. There's a cultural rule within the Ruby community that in order for code to be considered clean, we all have to be on the page as developers in regards to these standards. When observed, I find that Ruby code can be as painless (if not more so) to read than even Python. And of course it's much prettier than any Java or JS that I've ever seen. Libraries like ActiveRecord that are designed to conform to standard Ruby best practices make me extremely happy. 

Of course, things like naming conventions might seem trivial to some, but I think there's an art to layout, naming, and the structure of any application. When it's done well you can write code that feels organic, even when you're making changes and updating, which normally can be an endlessly maddening process. 

Whatever ORM tool you end up falling in love with, don't be like me. Embrace the community of brilliant coders around you, and embrace their time-tested solutions rather than trying to reinvent the wheel. 
