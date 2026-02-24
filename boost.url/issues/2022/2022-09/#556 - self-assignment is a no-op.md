# #556 - self-assignment is a no-op [Closed]

> Username: vinniefalco  
> Created at: 2022-09-16 23:20:38 UTC  
> Updated at: 2022-09-19 21:20:28 UTC  
> Closed at: 2022-09-19 21:20:28 UTC  
> Url: https://github.com/boostorg/url/issues/556  

Basically all containers with `operator=` need to check for self-assignment and do nothing.
