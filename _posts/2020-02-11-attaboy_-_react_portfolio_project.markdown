---
layout: post
title:      "Attaboy! - React Portfolio Project"
date:       2020-02-11 16:02:51 -0500
permalink:  attaboy_-_react_portfolio_project
---


### Part I - From False to Farts 

**That time I accidentally fixed my broken code with nonsense**

Let me start with a quick project description. As a member of the pet industry I've always been in love with training. Attaboy! takes the concept of very simple reinforcement-based training to make a tracking app for each of your dogs and their respective skills. The application backend is extremely light (the data being used is very simple, but the potential applications of that data are powerful indeed) it utilizes only three resources: 
* Dogs
* Skills
* Notes 

If you want to know more about the actual application interface, you'll need to [view my repository](https://github.com/Eli1771/dog-trainer)! For now I'll just say that React is my favorite. Of all the frameworks I've been exposed to none have felt as natural to write or as predictable to debug. Of course... there's an exception to every rule. 

Far from my only big bug, my *first* major head-scratcher happened after most of the backend scaffolding was already in place. The small handful of CRUD actions were in place, and each was updating to the store correctly:


| | Create | Read | Update | Delete | 
|-----|----|----|----|------|
| Dogs | X | X | | X| 
| Skills | X | X | X | X | 
| Notes | X | X |    |   |    |


My database was seedeed with test data, and my frontend was fetching dogs on each page reload. If I hit refresh at `/dogs`, everything would be fine, but if I refreshed the page at `/dogs/:id`, the app would crash and I'd get a `Cannot read property 'ID' of undefined` thrown at me. I knew that, even though I had *thought* everything was set up correctly - the store had a default `:loading` state of false and my components knew what to do with the page if loading became true like it was supposed to during `componentDidMount()` - but still my Dogs component was trying to read information about dog objects that hadn't arrived yet. 

*Let me stop here to say that some of you might already see the solution a mile off, but hold your peace; I got there eventually. *

I started trying to put some signposts on my loading state. Apparently I had forgotten that the `debugger` keyword exists (I seem to have to selective amnesia about that), because I went with the old faithful debugging strategy of the `console.log` (If you're an excessive console.logger like me and you happen to us Atom as your preferred text editor, you might consider installing the [console-log](https://atom.io/packages/console-log) keyboard extension). 

I put many log statements throughout my code to try and track the lifecycle of my loading state, but the Redux store always updates asyncronously, which made it hard to get my head around the execution order. I changed strategies. I would change the default value of the loading state so that I could see in console *exactly when the defualt value was assigned*. Now, I was admittedly a little slap-happy from too many hours in front of the screen and too many cups of coffee, so instead of using a more industry-appropriate placeholder to replace the value of 'false' ('true' probably would've been a good idea), I used the first infantile nonsense-word to pop into my head. I blame the hard *f* sound in false. 

When I hit refresh I was amazed to see not a *different* error as I had expected, but no error at all. The right side of my screen contained a very amusing console output, and on the left side was a working application.

![](https://i.imgur.com/A60642X.png)

Guess what, folks. *A string with more than zero characters is truthy*. 

```
state = {
  dogs: [],
  loading: 'farts',
  dogFormShowing: false
}
```

**My Mistake**

You may have already figured it out, but I'll spell it out anyway. Remember that the Redux store updates *asyncronously*. When I tried to load a route like `dogs/1` the following kept happening:
1. The component mounted
2. `componentDidMount()` executed the `fetchDogs` action. 
3. In the milisecond or so after `fetchDogs` was called, but before the first line - setting the loading state to true - caused the store state to change, my conditional statement that I was using to check if the dog objects were available saw that the loading state was  still false. 
4. Instead of returning a `<h3>Loading...</h3>` placeholder, my `renderDogs` functions started mapping all of the dogs in the store. There weren't any. 

It's all too easy to forget how many times components attempt to render: 

![](https://i.imgur.com/uOuCjHV.png)

I was able to fix the bug by changing the default loading state to true (not 'farts', sadly), and adding an extra conditional to my render function in order to avoid my loading placeholder just sitting on the screen... and sitting... 

`if (this.props.loading && !this.props.dogs.length) {`

I had to make sure the dogs were listed as loading *and* that there weren't any dogs already in the store waiting to render. This made the final render function look something like this: 

```renderDogs = dogs => {
    if (this.props.loading && !this.props.dogs.length) {
		    return // loading placeholder
		} else {
      return dogs.map(dog => {
        return // dog component
      });
    }
  }
```

Now if you prefer to stay a little truer to the lesson from the Learn curriculum, I'll point out that you can always set the default loading state to false just like we were taught and change the conditional above to `if (this.props.loading || !this.props.dogs.length) {...`.



**My Point**

Obviously this blog post is a lot more anecdotal than instructional, but I love sharing my relationship with developing. I learn the same lessons over and over and over again and I never get tired of it. Many of the other bugs that I encountered during the development of this application ended up requiring solutions that were equally surprizing (to me, at least), if not as entertaining. 

The moral of the story is this: Keep at it! Debugging is often a frustrating process, but one of my favorite things about development is how code can sometimes behave in the most charmingly unpredictable ways. 

### Part  II - Styling: A Love/Hate Relationship

I'll be breif.

I've always had a love/hate relationship with CSS. This caused me, in my early coding days, to cling to the few attributes of styling syntax that I actually understood and to fear anything new, i.e. flex and grid displays, Sass and - most of all - animations. Slowly, through Learn's curriculum and lots exposure to [codepen](https://codepen.io/) and the many 'pure CSS' masterpieces people have created there, the scales have begun to tip. I'm finally starting to appreciate the power of frontend styling... *almost* as much as I dislike it. 

I've taken this project as an opportunity to suck it up and dig deep. I've learned how to configure Sass (wish I would've done *that* a long time ago), I've used multiple advanced display techniques, and I even created some page-transition-animations. All this was as satisfying in the end as it was intimidating in the beginning! I want to say that I'm very happy with how the app *functions*, but I'm aware that there's still plenty more that I could add, which I hope to do very soon. In contrast, I feel there's really not much more that I could add to Attaboy's *presontation*. At least, that is, in *this* humbe non-professional web designer's opinion.

Needless to say I am deeply proud. Please check it out. 

![](https://i.imgur.com/UDx0I3s.png)

Here's just a couple of the resources and tools that helped me to design the frontend! 
* [Coolors](https://coolors.co/) - A "random" color-scheme generator with a ton of helpful tools to allow you to realize your design vision
* [Sass basics](https://sass-lang.com/guide) - To help you configure Sass in your application. If you're not familiar, it's a somewhat refactored CSS, and the nesting and variable capabilities alone make it worth almost anything
* [Page Transitions for Everyone](https://css-tricks.com/page-transitions-for-everyone/) - I've used the CSS-Tricks site in general more times than I can count, and this article  by Georgy Marchuk includes a link to the Swup library, which is a load-and-go library specifically for making it easy to create beautiful page-transition animations. That being said, I built my animations from scratch, and I wouldn't have been able to get a good handle on how to approach that without this article
* [Photopea](https://www.photopea.com/) - The photo editing app that I used to create the page logo. If you're not really a graphic designer (like me), this is a free web application with an almost shocking amount of functionality for editing and designing. I've used it many times for little projects and it's worth learning how it works! 


*That's all for now, although there's probably more blog posts to be written about the process of building Attaboy. Thanks for reading and happy coding!* 
