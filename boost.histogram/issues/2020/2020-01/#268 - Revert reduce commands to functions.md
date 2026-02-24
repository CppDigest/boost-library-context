# #268 - Revert reduce commands to functions [Closed]

> Username: HDembinski  
> Created at: 2020-01-16 08:23:46 UTC  
> Updated at: 2020-03-10 01:57:06 UTC  
> Closed at: 2020-03-10 01:57:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/268  

It is clearer and more future-safe if shrink, slice, etc. are functions that generate a reduce_command object.
