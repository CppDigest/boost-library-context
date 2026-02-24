# #1 - operator= wrong signatures [Closed]

> Username: vinniefalco  
> Created at: 2019-09-20 14:34:04 UTC  
> Updated at: 2019-11-14 15:15:30 UTC  
> Closed at: 2019-11-14 15:15:30 UTC  
> Url: https://github.com/boostorg/json/issues/1  

There should be 2 overloads: `operator=(string const&)` and `operator=(string&&)`. The same is probably needed for the constructors, and other places where `string` appears as a parameter type. And this may be needed for `array` and `object` overloads.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-14 15:15:30 UTC  
> Url: https://github.com/boostorg/json/issues/1#issuecomment-553932372  

This is now taken care of by a templated `operator=` which constructs a new value and then swaps.
