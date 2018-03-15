# 3

## 1.1 Promises API

ES6, and many Javascript libraries before it, bring us promises.

#### What is a promise?

A promise is **a built-in object** (usually you receive them from functions, like calling an API)

It **has a state**, like 

- **fulfilled** - The action relating to the promise succeeded
- **rejected** - The action relating to the promise failed
- **pending** - Hasn't fulfilled or rejected yet
- **settled** - Has fulfilled or rejected

You **attach callbacks to the end states of the promise**, like “when resolved, doStuff()…”

**When the promise state changes, those callbacks run**

And **you can chain those callbacks together** using methods on the promise object, especially `.then()` 



#### What are you talking about?

We can create a sample promise like this:

```javascript
let promise = new Promise(function(resolve, reject) {
  resolve("Mary had a little lamb... ");
});
```

We create this new promise already "resolved"; now its time to chain it.

```javascript
promise
  .then(function(lyrics) {
  console.log(lyrics); // should be something about Mary here
	return lyrics + "her fleece was white as snow... ";
})
  .then(function(lyrics) {
  console.log(lyrics); // very clean lamb
  return lyrics + "and everywhere that Mary went... "
})
  .then(function(lyrics) {
  console.log(lyrics); // this lamb is attached!
  return lyrics + "the lamb was sure to go... "
}).
  then(function(lyrics) {
  console.log(lyrics);
}); 
```

You notice that the chaining is not nested. What's going on? Each `.then()` returns a new promise, so we can keep calling `.then()`, and passing data, until we are done.



#### Why use promises?

Reasons:

           - you will see promises all over Javascript projects
           - They provide an API for handling chains of callbacks; the API helps us flatten our call chain, handle our errors, and reason consistently about (callbacks are DIY)
           - They are built-in to ES6 and used as a primitive in many features and APIs, such asES7 async functions (via the await keyword), many implementations of observables, etc. (cool stuff!) 

 

But callbacks also can get complicated. Let’s imagine that we wanted to make money from our movie API. Every call to the movie API could look like this:

```javascript
getMovieData(function(error, success){
   if(error) {handleError();}
   else {
	getMovieRecommendations(function(error, success){
		if(error) {handleError();}
			else {
				getMovieAds(function(error, success){
					if(error) {handleError();}
						else{
							getWhoWatchingMovieNow(function(){
//OMG!
                        });
                    }       
                });
            }//who's bracket is this?
        });
   }    
});
```

Why? Because every function here depends on the results of `getMovieData()`. Everything must react to the return of that function.  This is “callback” hell.

In a simplified form, we are looking at this kind of structure:

```javascript
async1(function(){
   async2(function(){
        async3(function(){
            async4(function(){
                ....
            });
        });
   });
});
```

 

But promises can help us avoid this. Consider:

```javascript
fetch(url).then(onError1,onSucceed1)
   .then(onError2, onSucceed2)
   .then(onError3, onSucceed3)
   .then(onError4, onSucceed4)
   ... 
```

And our more realistic example:

```javascript
getMovieData.then(error, getMovieRecommendations)
   .then(error, getMovieAds)
   .then(error, getWhoWatchingMovieNow)
   ...
```

So promises aren’t too bad.

Let's port our code to them!