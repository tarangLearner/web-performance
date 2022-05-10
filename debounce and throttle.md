Debouncing and Throttling are two widely-used techniques to improve the performance of code that gets executed repeatedly within a period of time.
In this post, we’ll learn how to better use them in order to boost our app’s performance and write better and faster code in JavaScript!

Debouncing
    Debouncing is a programming practice used to ensure that time-consuming tasks do not fire so often, that it stalls the performance of the web page. The debounced function will ignore all calls to it until the calls have stopped for a specified time period. Only then will it call the original function.
    Debouncing forces a function to wait a certain amount of time before running again. In other words, it limits the rate at which a function gets invoked.
    Implementing Debounce:
    The general idea for debouncing is-
    1) Start with 0 timeout.
    2) If the debounced function is called again, reset the timer to the specified delay.
    3) In case of timeout, call the debounced function.

    Thus every call to a debounce function resets the timer and delays the call. Let’s dive in and see what a debounce looks like-

We are passing a function(func) and a delay(delay) into the debounce function. timer is a variable that we use to track the delay period.


const debounce = function(func, delay){
      let timer;
      return function () {     //anonymous function
        const context = this; 
	const args = arguments;
	clearTimeout(timer); 
	timer = setTimeout(()=> {
	    func.apply(context, args)
	},delay);
       }
}

Debounce is a higher-order function, which is a function that returns another function. This is done to form a closure around the func and delay function parameters and the timer variable so that their values are preserved.

If we are invoking for the first time, our function will execute at the end of our delay. If we invoke and then invoke again before the end of our delay, the delay restarts.

Here’s how that debounce looks in action.


debounceBtn.addEventListener('click', debounce(function() {
  console.info('Hey! It is', new Date().toUTCString());
}, 3000));



In this example, we are debouncing the call by 3 seconds at which point we print the date.

Autocomplete — Often times, search boxes offer dropdowns that provide autocomplete options for the user’s current input. 
Sometimes the items suggested are fetched from the backend via API (for instance, on Google Maps). 
Here, debouncing can be applied in implementing suggestive text where we wait for the user to stop typing for a few seconds before suggesting the text. 
Thus, on every keystroke, we wait for some seconds before giving out suggestions.

Other use cases —
1. Debouncing a resize event handler.
2. Debouncing a save function in an autosave feature.
3. Don’t do anything while the user drags and drops.
4. Don’t make any Axios requests until the user stops typing.

Throttling
    Throttling or sometimes is also called throttle function is a practice used in websites. To throttle a function means to ensure that the function is called at most once in a specified time period (for instance, once every 10 seconds). This means throttling will prevent a function from running if it has run “recently”. Throttling also ensures a function is run regularly at a fixed rate.
    Throttling is used to call a function after every millisecond or a particular interval of time only the first click is executed immediately.
Implementing Throttle:
    Throttle can be a little taxing as its desired behavior has different interpretations. Let’s start by limiting the rate at which we execute a function.
    
 const throttle = (func, limit) => {
  let inThrottle;
  return function() {
    const args = arguments;
    const context = this;
    if (!inThrottle) {
      func.apply(context, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  }
}


The first call to our function will execute and sets the limit period inThrottle. We can call our function during this period but it will not fire until the throttle period has passed. Once it has passed, the next invocation will fire and the process repeats.

But what about our last call? If it’s in the limit period it’s ignored and what if we don’t want that? We need to catch this and execute it after the limit period.

const throttle = (func, limit) => {
  let lastFunc;
  let lastRan;
  return function() {
    const context = this;
    const args = arguments;
    if (!lastRan) {
      func.apply(context, args)
      lastRan = Date.now();
    } else {
      clearTimeout(lastFunc);
      lastFunc = setTimeout(function() {
          if ((Date.now() - lastRan) >= limit) {
            func.apply(context, args);
            lastRan = Date.now();
          }
       }, limit - (Date.now() - lastRan));
    }
  }
}


This implementation ensures that we catch and execute that last invocation. We also invoke it at the correct time. We do this by creating a variable lastRan which is a timestamp of the last invocation. We can then use this to determine if the last invocation took place within the throttle limit. We can also use lastRan to determine whether the throttled function has ran at all. This makes the previous variable inThrottle redundant.

One way to think about this implementation of throttle is like a chaining debounce. Each time the debounce waiting period lessens. throttle has some interesting possibilities. For example, you could store all the ignored executions and run them all at the end in order.


Examples:
Gaming — In action games, the user often performs a key action by pushing a button (example: shooting, punching). But, as any gamer knows, users often press the buttons much more than is necessary, probably due to the excitement and intensity of the action. So the user might hit “Punch” 10 times in 5 seconds, but the game character can only throw one punch in one second. In such a situation, it makes sense to throttle the action. In this case, throttling the “Punch” action to one second would ignore the second button press each second.

Scroll event handler — Another application of throttling is in content-loading webpages like Facebook and Twitter where the user keeps on scrolling. In these scenarios, if the scroll event is fired too frequently, there might be a performance impact, as it contains lots of videos and images. Thus the scroll event must make use of throttling.



Other use cases —
1. Throttling a button click so we can’t spam click
2. Throttling an API call
3. Throttling a mousemove/touchmove event handler.


Conclusion
A debounce is a cousin of the throttle, and they both improve the performance of web applications. However, they are used in different cases. A debounce is utilized when you only care about the final state. A throttle is best used when you want to handle all intermediate states but at a controlled rate.



