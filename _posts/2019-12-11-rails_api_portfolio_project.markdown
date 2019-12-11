---
layout: post
title:      "Rails API Portfolio Project"
date:       2019-12-11 02:21:11 -0500
permalink:  rails_api_portfolio_project
---

*Meal Mate*


Wow, this one took a long time! It's been a busy season for me and making this app sure was a journey, but I'm extremely proud of how Meal Mate turned out. I'll get right to the project, and I'll try to keep this post pretty brief (knock on wood), but there's a couple things I want to talk about. Here goes! 

**The Concept**

Meal Mate was the first portfolio project that wasn't specifically created for a friend or family member in order to assist with some business problem of theirs. I was inimidated by the lack of direction at first, but I've enjoyed the freedom to try come up with something that could be as fun and as pleasing as it could be useful. 

Here’s my inspiration: I love cooking and I love trying new things, but I hate the planning aspect of meal-prep. I wanted an app that would take all of the decision-making out of making a meal plan while ensuring that I still get to try new things and experiment in the kitchen. My app allows users to select a week's worth of meals from a 'map', and with one click of a button a meal plan - complete with links to recipes and a compiled, formatted shopping list - will be randomly generated from a diverse database of recipes. 

As a quick aside I want to point out that I kept this generator *extremely* basic. There are many, many, **many** features one could conceivably add to make this app more functional and accessible (adding original meals, generating plans based on specific parameters, or adding dietary or ingredient restrictions to name a few), and I won't go into any of them in detail here. I wanted to submit a product that I was proud of without breaking my back unnecessarily. After all I've still got plenty of curriculum to get back to! Suffice it to say that I love my project's potential, and I will be getting back to it ASAP! 

Here are the steps to create a meal plan for the week.

1. A user determines which meals over which days they would like Meal Mate to generate by selecting buttons from a 'map' of 21 potential meals. 

2. A user clicks the stylized 'Create Your Plan!' button on the CSS oven.

3. That's it! Now the user has a 2-pane week plan (displayed in the cute little open oven) that displays each recipe by day in one pane, and a compiled list of ingredients (with quantities) that they will need in the other. 

The app uses the native Date object to determine if there are any meal plans for the current day in the db and displays what it finds with more detail in a separate pane for ’Today’s plan'. In future updates, this is where users will be able to rate recipes based on overall taste, cost, nutrition, etc., so that they can generate plans 'pseudo-randomly’ based on desired parameters.


**Building a Randomizer**

For the more technical side of this post I’ll try to only talk about things that I found outside of Flatiron's curriculum to keep things interesting. The most obvious 'brain' of meal mate is the random generator. I knew this generator would be complicated no matter what, so I made things easier on myself in a couple ways. First, my db structure presupposes that each recipe applies to *only one meal* (when I say 'meal', I'll always be referring to Breakfast, Lunch or Dinner, not any specific recipe). We all know that many foods can be eaten at different times, but making this assumption simplifies things quite a bit. I also created the same number of recipes for each meal. There are certainly ways to do what I need to without making the same number of recipes for each meal. I think there’s a way to accomplish just this by using SQL queries in my ruby models to return serialized JSON that would represent recipe counts for each meal, but for now I wanted to keep this particular function as simple as possible. Later I will change this so I can add as meals as I want without having to worry about it breaking my generator. Lastly, each meal applies to one sitting. I'd didn't try to bother with making meals that could create leftovers or worrying about serving sizes, although that would be some amazing functionality for future versions. 

I started with scoping. I made a simple nested route structure that would allow me to view an index of recipes based on which meal they belong to. 

```
resources :meals, only: [:show] do
    resources :recipes, only: [:index, :show]
  end
```

Now I can fetch, for example, all my lunch recipes with the URL 'http://localhost:3000/meals/2/recipes'. Great! Now I just have to randomly select a number between 1 and the amount of each meal and that will correspond with the ID I need, right? Wrong. Even if you scope an index action to only contain certain objects within some parameter, each object's ID is still its own ID. If I have 20 breakfasts and my random number comes out to 12, then the recipe that will be selected will be the 12th recipe to enter the db, regardless of other params. *If*, that is, you follow restful principles in your rails controllers. It looks like I need some customization. 

Ordinarily the Show action within a Rails controller for a nested route like the one above would look like any other, since ID's ignore whether they're in a particular URL's scope.

```
def show
    recipe = Recipe.find_by(id: params[:id])
    render json: recipe
end
```

However, this particular situation requires a bit more customization. 

```
def show
    if params[:meal_id]   # Check to see if the route is nested
      recipes = Recipe.meal(params[:meal_id])   # Get all recipes within desired scope
      recipe = recipes[params[:id].to_i]   # Get the NTH recipe in the recipes collection, NOT the recipe with an ID of N
    else
      recipe = Recipe.find_by(id: params[:id])
    end
    render json: recipe
end
```

Excellent. I'm *almost* there, but not quite. From my JS frontend I can request JSON through a fetch call, but there are certain things I can't get. If I want a specific recipe, I *have* to know it's actual ID. If I want to know the ID of the 'nth' recipe within a certain meal, I need a sort of 'helper' fetch. I have the ID of the meal (breakfast/lunch/dinner) I need a recipe for and I have a random number between 1 and the recipe count: `fetch(`${BASE_URL}/meals/${mealId}/recipes/${rIndex}`);`. From here I can capture the ID of the returned JSON object, but I'll talk about how I do that shortly. In any case, the rest of my JS is very straightforward. I simply create a new object, an instance of RecipePlan (a sort of join-object between recipes and day-plans), using the ID of the recipe from the JSON response. 

There's only one more hurdle we have to jump over to make this process work the way we need it to. Each meal that I'd like to generate is based on a random selection of a recipe from the corresponding meal, but anything that is truly random can produce flukes. The last thing I want is for someone to have to make chicken and biscuits four times in one week because my randomization doesn't have any guidelines. In future updates, there may be room for *some* limited level of repetition in meal plans, but for now I'll just say that no recipe should appear twice within the same meal plan.

Now. I'll be the first to admit that my approach to solving this problem might not be the most technologically elegant; it's just a trick. But I'll *also* be the first to admit that I absolutely *love* coming up with clever little tricks to solve complicated problems in JavaScript. 

This is beside the point, but if you choose to check out my repo and launch it in chrome, you’ll see that I had a ton of fun creating a million little CSS/general frontend *tricks* that I use to make the page render in a more enjoyable way (you’ll see even more if you copy over my code from ‘future.js’ that I’ve set aside for coming updates). I live for this. I will never get tired of finding a new use for some function outside of its originally intended purpose. 

Back to the point: In my top-level plan-generation function, I define a variable called alreadyPicked. This is an array of three empty arrays. Each sub-array represents a meal, and the whole var will keep track of which recipes have already been randomly picked in order to prevent repetition. This top-level function generates the object that will store the entire plan for the week, while instantiating the empty `alreadyPicked` array. 

```
let alreadyPicked = [[],[],[]];
```

Then this function will loop through each day of the week, creating objects for each day-plan. The empty array is accepted as an argument, but here's the clever part: Within the parent function, alreadyPicked gets reassigned with the *result* of the day-plan generator function, and then fed back into the next loop-cycle. I just have to remember to return the updated array of already chosen meals at the end of each iteration.

```
async function generateDayPlan(...args, alreadyPicked) {   // A function to generate each day’s meal plan, NOT the parent function that generates the whole week’s plan
  if (!whichMeals.reduce(noneChecked, true)) {   
    let dp = new DayPlan(weekPlanId, (day + 1), date);
    let configObj = dp.configObj

```
...
```
    for (let meal = 0; meal < 3; meal++) {
      if (whichMeals[meal]) {
        let rIndex = rand12() - 1   // creates the random index
        while (alreadyPicked[meal].includes(rIndex)) {
          rIndex = rand12() - 1   // keeps picking a random number until it finds one it hasn't found before
        }
        alreadyPicked[meal].push(rIndex);   // adds the new index to the array
        await associateRecipe(rIndex, dp.dayPlanId, meal + 1);   // function to create the recipe-plan join object
      }
    }
  }
  return alreadyPicked   // send the updated alreadyPicked aray back to the parent function to keep looping 
}
```

I love using little helper objects like the alreadyPicked array, but you might be wondering why I made it so complicated. After all, why make an array of three arrays when recipe ID's are already unique? I did entertain just that idea at first; every time you select a recipe, store its unique ID in *one* array. But unfortunately there's an extra step to finding that unique array. I want to utilize as few server requests as possible while ensuring that my app functions properly, and it takes a while for a fetch statement just to find the unique ID I need in order to grab a recipe. I'd rather use a more complicated helper object that factors in the uniqueness of the randomly-generated ID *and* the ID of the meal (brkfst/lnch/dnr), then have to make potentially infinite server requests while trying to find an ID I haven't already chosen. 

I'd like to make one last call to action before moving on to my final point. I am alllllmost certain there's a better way to do this. I don't know if it's a question of better db relationships, some clever SQL call or just better JS case handling, but my experience has taught me that there's always room for improvement. I would *love* to know what someone else's approach to a random-and-unique style algorithm might be. I had tons of fun with this part of the app, but I really think I’d have even more fun hearing your approach! 


**The Async/Await Structure**

And now, my final thoughts.

I'm definitely curious to know just how helpful others find this, but it sure blew my mind. Now don’t get me wrong: I think the fetch function is amazing. A part of me wants to hate it, because *every fetch request returns a promise no matter what*. But the reality is that JS promises make the world go round. Promises allow people to create complicated nests of interconnecting, interdependent procedures that fire when previous chunks of code have executed succesfully. Amazing! That being said, no one will convince me that promises aren't clunky. It would be terrific if you could create a fetch request that rendered a response into JSON (like normal), sent that JSON into a callback function (like normal) and then sent the *result* of that function back into the *original* function where the fetch request happened in the first place, i.e. `let name = fetch(http://my-url.com/people/2').then(resp => resp.json()).then(json => json.name)`.

Actually, that works... sort of. The fetch request - after each call of .then - returns a JS promise. This is a highly specialized JS object that is integral to making asynchronous functionality happen. Each promise has a status attribute - if all goes well it will be 'resolved' once the applicable request has executed - and a value attribute, which usually also points to an object. Unfortunately, you can't just assign a promise to some variable and then access its value like a basic JS object. In the above example, `name` is equal to a promise. But you couldn't instead assign the above expression to a variable like `jsonPromise` and then set `name` equal to `jsonPromise.value`. The values of promises can only be accessed by a call to .then() by passing the promises value into a *callback function*. It is possible in this way to assign a specific value of a specific JSON attribute of some fetch call to a variable, but it's complicated. It requires creating a lot of messy callback functions, and a mastery of .call(), .apply(), and .bind() in order to control scope. 

I found out about all this way, way, way after I accidentally discovered a much simpler approach; the async/await structure. I'll make this very quick because async/await is quite simple to get the hang of, and because I just provided a *ton* of lead-up to what is truly a fairly simple hack (seriously though, look into promises when you have the time, they're a pain to get used to but they're an amazing tool).

First: you can't use the `await` keyword outside of an asyncronous function. What's that you say? It couldn't be easier, just put `async` before your function declaration: `async function someFunction() {`... Next is await. This keyword essentially replaces the .then() function. It says 'Execute some code. Once it's done, take the *value* of that execution, *not* in the form of a JS promise, and return it'. This allows you to extract standard JS data from fetch calls and assign them to variables just like anything else.

```
async function someFunction() {
   const resp = await fetch(`${myUrl}/posts});
   const json = await resp.json();
   return json;
}
```

Obviously this isn't the only way to work with AJAX and JSON, but when you're working with a more lightweight querying syntax like SQLite3, you stand less of a chance of locking up your db by overloading it with too many async calls. Also, I just happen to think the async/await pattern has much better readability. 

I don't always use async/await, but I think differentiating between use cases might be for another blog post and another day. 

If you'd like to delve more into my JS or just try out Meal Mate for yourself, check it out at https://github.com/Eli1771/meal_mate. I'd love to hear your opinions on this post, differences on how you'd approach solving some of the problems I've discussed and features you'd like to see added to the Meal Mate app over time. 

Thanks for reading and happy coding! 
