# #37 - Coroutine primer > Event loop typo [Closed]

> Username: anarthal  
> Created at: 2023-07-24 08:20:39 UTC  
> Updated at: 2023-07-24 09:36:39 UTC  
> Closed at: 2023-07-24 09:36:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/37  

> Since the coroutines in async can co_await events, they need to be run on an event-loop. That is another piece of code is responsible for tracking outstanding events and resume a co_await coroutine  
  
Suggestion: "This is another piece of code responsible for tracking outstanding events and resuming coroutines `co_await`-ing them.
