# 2

## 1.1 Playing with a RESTful API

Tonight we’re going to think about async in the context of a particular task: Using someone else's hard work! 

We’re going to use a simple API called the Open Movie Database (omdb @ http://www.omdbapi.com/).

```
Get a FREE API key for omdb:
http://www.omdbapi.com/apikey.aspx
```

With a (free!) API key, we can start playing around.

#### RESTful, JSON, HTTP... blah blah blah

**Omdb is a RESTful JSON API… what does that mean?**

**REST… ful?**

> REST stands for REpresentational State Transfer. The acronym is more friendly, right? REST is a loose style for creating web APIs. And when we say “web”, we really mean “HTTP”. And when we say “HTTP”, we mean talking to a web server with strings. 

**JSON?**

> When we say JSON, we mean Javascript objects. And when we say “API”, we mean URLs that give you back data, not webpages. 

A RESTful API works something like this: 

*From our side...*

We talk to an HTTP server by sending a Request. Requests are text messages that we classify with **HTTP verbs**, like **GET** and **POST**. Each Request is a request for something - that's the **URL**, which names the target of our request. And inside these Requests, we can store some data (often **key-value pairs** in the URL or a bundle of strings in the HTTP packet.

*On the server’s side...*

The server checks out the HTTP verb and URL. If it understands what we want, it calls a database, a cache, a filesystem, or some kind of data store and retrieves what we want. It sends us back this data as a Response, with an HTTP status code, like 404 or 200, to signal to us what has happened.

#### Let's try it

Let’s just try this API out. We’re going to use a very advanced tool called the “browser address bar”. This is where you whisper all of your deepest darkest secrets, but it's also a handy tool for making simple GET requests!

So what do we need to call the API? Well we need a URL. But what goes in the URL?

- a domain,obviously
- some parameters, or key-value pairs that tell the API what we want and who we are

**Can anyone tell me what is the minimum I need for this API? (Hint: read the homepage of the API)**

```html
http://www.omdbapi.com/
?t=cat
&apikey=d9429fd1 <!--super secret-->
```

**Ok, and can someone tell me what all the parts of this string mean and are doing?** Notice the details!

Great, so we can run this right in the browser. And we get back… 

```javascript
{"Title":"Black
Cat, White
Cat","Year":"1998","Rated":"R","Released":"01
Jun 1998","Runtime":"127
min","Genre":"Comedy, Crime, Romance","Director":"Emir
Kusturica","Writer":"Gordan
Mihic","Actors":"Bajram Severdzan, Srdjan 'Zika' Todorovic,
Branka Katic, Florijan Ajdini","Plot":"Matko is a small
time hustler, living by the river Danube with his 17 year old son Zare. After a
failed business deal he owes money to the much more successful gangster Dadan.
Dadan has a ...","Language":"Romany, Serbian,
Bulgarian","Country":"Federal Republic of Yugoslavia,
France, Germany, Austria, Greece","Awards":"5 wins & 4
nominations.","Poster":"https://images-na.ssl-images-amazon.com/images/M/MV5BMmExZTZhN2QtMzg5Mi00Y2M5LTlmMWYtNTUzMzUwMGM2OGQ3XkEyXkFqcGdeQXVyNTA4NzY1MzY@._V1_SX300.jpg","Ratings":[{"Source":"Internet
Movie
Database","Value":"8.1/10"},{"Source":"Rotten
Tomatoes","Value":"83%"},{"Source":"Metacritic","Value":"73/100"}],"Metascore":"73","imdbRating":"8.1","imdbVotes":"44,560","imdbID":"tt0118843","Type":"movie","DVD":"04
Jan
2000","BoxOffice":"N/A","Production":"October
Films","Website":"N/A","Response":"True"}
```

What a hot mess! What is this?

It’s JSON. It’s really a piece of Javascript, just crammed together in an ugly string. Not pretty and formatted, but an IDE can fix that.

There aren’t any functions, just properties, maybe an array. But it’s not bad because the Javascript runtime can just eat this up. Let’s feed the Javascript kitty here (in Chrome console, add `let result =` and copy paste that mess).

Okay, so it’s in there. From inside the console we can call the properties of this result object, like `result.Ti`… oh code completion… mmm, delicious… `result.Title`, `result.Actors`, `result.Ratings[1]`… nice.

#### Let's try it with Javascript 

Let’s stop leaning on the address bar here and make this work in Javascript.

We’re going to use an old and familiar API, called `XmlHttpRequest`, or `XHR`. That "HTML Request" part is super useful for us (the "XML" part we will just ignore, like it never happened). `XHR` allows us to make HTTP calls; we have a little more control here than with the address bar. 

We’ll create an `XHR` object, use it to call the API, and print the results out to the screen (not the console, a REAL screen!). Let’s quickly cook this up; it will be dirty but working.

```html
<!DOCTYPE html>
<html>
<body>
   Search: <input type="text" id="movieTitle"><br>
   <button>Search </button>
   <div id="title"></div>
   <div id="poster"></div>
</body>
</html>
```

So, we’ll call the `omdb` API, with our API key, and get just one movie using a keyword. And we’ll then put the movie’s title and poster tags here. A little form here will take this input, giving the user some control.

Working backwards here, let’s hook up an event to this button:

```html
<button onclick="displayMovie()">Search </button>
```

So the plan is... when we click this button, the browser should call this function. And this function will make everything happen. 

Let's define that function now. 

First thing this function needs is to get the form input, so we can build our URL.

```javascript
function displayMovie() {
	let keyword = document.getElementById("movieTitle").value;
	//lots of TODOs here
}
```

 Good, now we’ll put this keyword into a URL string:

```javascript
let url = "http://www.omdbapi.com/?t=" + keyword + "&apikey=d9429fd1";
```

And let’s just imagine that we have a function, `request()`,that returns the API results to us:

```javascript
let json = request(url); //someone please make this function, thanks!
```

now the results will be in JSON, so we can use a built-in method and parse the JSON string into an object (deserializing it):

let results = JSON.parse(json);

Oh it's getting serious now!

Okay, now where will these results go? Well, we know we want `results.Title` and `results.Poster`. Let’s write them directly into the webpage:

```javascript
document.getElementById("title").innerHTML = `${results.Title}"`;
```

And the image goes into an img tag:

```javascript
document.getElementById("poster").innerHTML = `<img src="${results.Poster}" alt=${results.Title}>`;
```

This function won’t return anything; it will take one string as input, call the API, and post the results to two tags in the webpage.

But all of this depends on that request() function. That’s kind of automagical right now. Let’s implement it.

```javascript
function request(url) {
	let r = new XMLHttpRequest();
	r.onreadystatechange = () => {
		if (r.readyState == 4 && r.status == 200) {
		console.log(r.responseText); //print to console just for fun
		}
	}
	r.open("GET", url, false);
	r.send();
	return r.responseText;
}
```

`XHR` is a built-in object. It’s methods give us an API to make HTTP requests. We’ve wrapped this `XHR` in a function – at top we create it, in the middle we configure it, and at bottom we return a result.

We use the `open()` method to set up a connection, the `send()`method to execute the call. And we also have this `onreadystatechange`, which is a property. But we’ve assigned it to a function, so it can do anything! What happens is, when the state of this `XHR` changes – for example, when our request succeeds and we get an HTTP status code of 200 – the `XHR` object will call this function. 

That sounds a little asynchronous - like a callback! And look, here’s our callback – it’s writing to the console. That’s nice. 

But the real goods come from that return statement at the bottom. The `responseText` is the JSON we want. That’s the payload. 

Okay, all of this goes in a script tag. And we run the page in the browser. 

Works! 

#### But is it async?

You might notice that your browser warns you in the console,you’re making a synchronous XHR. But but but… we have a callback!

 

Callbacks are just functions. What makes them async is whenthe runtime puts them into the asynchronous queue.

And here we are actually telling the Javascript runtime toNOT put them into the async queue. There’s a flag set to “false” in the XHR.open()method. This flag sets the XHR to operate synchronously, which means BLOCKuntil the request comes back.

No worries, let’s set it to false and run this.  

Hmm, that doesn’t work quite right. What’s going on? **Does anyone know why this is broken now?**

If you open your browser's console, you get some clues, but not an explanation.

So for code to run asynchronously, we have to push it to the async queue. But that’s not happening here… except for that `console.log()`, it still works. Good old `console.log()`! And now you can see what that `readystatechange` property gives us – it’s a hook for the XHR’s events.

`XHR` is pushing the callback there onto the async queue. And it calls when `XHR` returns something and gets passed (or has access to) the XHR’s methods.

But our DOM code isn’t hooked up to that. Our DOM code in fact runs BEFORE `console.log()`. It runs in the main loop, completely unaware that it “should” wait for the `XHR` object. We can see that because we get this JSON error in the console. There’s nothing in that variable for JSON to parse, and it complains.

**What’s the solution? How can we hook up our DOM code to the `XHR`?**

****Move all of the DOM logic into that callback. Specifically, we need that DOM logic to execute when the `XHR` is ready, not before, and we need the XHR to pass its results to that callback. Remember, async requires you to think about the order of execution and how data gets passed between functions.  

We’ve got some changes to make. **Can anyone recommend what to do?**

Let’s start with a new function:

```javascript
function render(response) {
	let results = JSON.parse(response);
	document.getElementById("title").innerHTML = `${results.Title}"`;
	document.getElementById("poster").innerHTML = `<img src="${results.Poster}" 		alt=${results.Title}>`;
}
```

We can rip out everything that deals with the results and move it into its own function. This is our callback that has to go on the async queue. 

And let’s comment out those portions from `displayMovie()`

```javascript
//let results = JSON.parse(json);
//document.getElementById("title").innerHTML= `${results.Title}"`;
//document.getElementById("poster").innerHTML= `<img src="${results.Poster}" alt=${results.Title}>`;
```

 

Now we’re committed! We only need to make one more change…render() needs to be in the callback:

```javascript
r.onload = function() {
	if (r.readyState == 4 && r.status == 200) {
	render(r.response);
	} 
	else {
	console.log('Woah, thats a big, fat HTTPERROR', r.statusText);
	}
};
```

Remember this callback function has a destiny - it was chosen to go to the async queue and be... *called back* later.

Let’s try it. Success! We’ve used a callback – really a plain old function – to asynchronously call the API and return the results.


