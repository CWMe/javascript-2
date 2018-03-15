# 1

## 1.1 Introduction to Async Javascript

Let’s begin with some sample code:

```javascript
for (var i = 1; i <= 3; i++) {
   var timer = setTimeout(function() { console.log(i) }, 0);
   console.log('iteration #' + timer);
};
```

You can run this in the browser console, JSFiddle, or anywhere Javascript is accepted.

You might notice, aside from having no useful purpose, it's also a little wonky. 

**Remember last week? What ES6 features could we employ here? (hint: Around three)**

#### What is setTimeout() doing?

But there’s something else about this code that’s strange… notice that the first console.log() runs *after* the second. Are you guys familiar with `setTimeout()`? What does it do?``

`setTimeout()` delays the execution of a function – the function that you pass into it as a parameter. And you set the "delay" in milliseconds. 

But look, we set the timer to zero, so when should it run? And `setTimeout()` definitely returned something, because we printed out the timer! 

So what’s going on? `setTimeout()` returns, but then its inner function runs later. Where does that inner function go while it waits for execution? Where's the wormhole here accomplishing this multiple invocation 

#### What's really going on?

At a high level, the following is really happening:

1. setTimeout() pushes the function to a queue and returns the timer id
2. All the code in the main Javascript loop executes next, in order
3. Once the main Javascript loop is finished, the Javascript runtime checks the queue, finds the callback, and runs it.

Behind the scenes, the runtime is **queuing functions and then later calling back to them**. Hence the term "**callbacks**".

We'll call this behavior "asynchronous execution" or async. This a fundamental way to run and write programs, but its not a syntax thing. Think about async two ways:

This is an example of how Javascript implements asynchronous execution (we’ll call it async). Now at heart, async is about two things:

1. the **order of execution**
2. the mechanism that passes **one asynchronous function’s return value to another function**



## 1.2 What is Synchronous? Asynchronous?

**Short answer:** Code that blocks other code from running.

In my mind, asynchronous implies “non-blocking”. And synchronous is the opposite. 

In the browser, where a lot of Javascript lives, works, and plays, synchronous code can have a disastrous effect. Consider this webpage with Javascript code:

```html
<!doctype html>
<html>
<body> <!-- thanks to https://github.com/rauschma/async-examples -->
    <p>
        <a id="block" href="">Block for 5 seconds (I dare you to click this)</a>
        <p>
            <button>Click me too!</button>
            <div id="statusMessage"></div>
            <script>
                document.getElementById('block')
                    .addEventListener('click', onClick);
                function onClick(event) {
                    event.preventDefault();
                    setStatusMessage('Blocking... patiently count with me to five...');
                    setTimeout(function() {
                        sleep(5000);
                        setStatusMessage('Your patience is rewarded! Not Blocking...');
                    }, 0);
                }
                function setStatusMessage(msg) {
                    document.getElementById('statusMessage').textContent = msg;
                }
                function sleep(milliseconds) {
                    var start = Date.now();
                    while ((Date.now() - start) < milliseconds);
                }
            </script>
</body>
</html>
```

Take a minute and check this out. Can anyone explain what is going on here? What the functions doing here? What's the order of execution? We have `setTimeout()` here, so is this async or synchronous?

Let's run it!

#### The synchronous contract

Let's articulate this behavior better. What is the contract between the Javascript runtime and a function running synchronously?

It's something like this... the Runtime says:

 **You are a function. When I call you, you will run. You will block other functions while you are running. But when you return, you are done.** 

That’s what we expect from our functions! The synchronous contract allows us to think linearly about the order of execution: The order of function calls, as we command, is the order that they run in. And passing data out of functions is simple: Pass function results in a variable or parameter (to another function).

We naturally describe work in synchronous steps, for example:

```javascript
//perfect and simple synchronous execution 
function prepareFriedChicken() {
	let egg = sourceEgg(); 
	let chick = incubate(egg);
	let chicken = raise (chick);
	let fatChicken = fatten(chicken);
	let meat = retire(fatChicken);
	return fry(meat);
}
```

But there's more than one thing to do. 

Notice the synchronous contract includes the word “block”. That sounds pretty serious, maybe even disagreeable!

What does block mean? The next function has to wait for the current function to finish completely. There is no sharing; functions block each other in order. That makes sense when we are making a single dish for dinner, for example, but not when we are cooking a whole meal:

```javascript
//real world asynchronous workload
function makeDinner() {
    prepareFriedChicken();
    prepareSalad();
    prepareCocktails();
}
```

Why should all of these block one another? We’re going to be waiting a *long time* for that salad. Give us the cocktails first! **WORST MEAL EVER!**

The synchronous contract is not enough. Javascript in the browser needs to work with the UI and the network. But calling one of these could block everything else, and calling them repeatedly would destroy the usefulness of a webpage (not to mention the advertising industry!).  

We need a new contract. That’s where the asynchronous contract comes in. 

#### The asynchronous contract

Now, based on our setTimeout() example before, can anyone describe for me the asynchronous contract? How could we articulate it?

We might describe it this way:

**You are a function. When I call you, you will run, you will place a callback on the callback queue and return immediately (NOT block). You will wait until I pull that callback from the queue. Then you may return a value to that callback. Then you are done.**

We can visualize it this way:

![https://blog.carbonfive.com/wp-content/uploads/2013/10/event-loop.png](https://blog.carbonfive.com/wp-content/uploads/2013/10/event-loop.png)

This is more complicated for us than the synchronous contract. Notice we now have to think about our two concerns more carefully:

- the order of execution (when will my code run? What order will it run in? when will I get the results?)
- how will I pass data between my functions?

**Can anyone think of some examples where Javascript exposes asynchronous features to us? Things like setTimeout()?**

Tonight, we're going to look at a simple case where we need asynchronous behavior and Javascript has some APIs to help us.

