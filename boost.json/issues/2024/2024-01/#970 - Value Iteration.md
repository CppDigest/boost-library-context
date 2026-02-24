# #970 - Value Iteration [Open]

> Username: doganulus  
> Created at: 2024-01-08 09:36:45 UTC  
> Updated at: 2024-01-12 10:55:40 UTC  
> Url: https://github.com/boostorg/json/issues/970  

I would like to start a discussion on having a class of `value_iterator`, which will be used to iterate over a `value` instance like visiting a tree in a depth-first manner.  
  
Currently, we can iterate over `array` and `object` but I think a more general iterate functionality will be helpful for many applications. Also it would be nice that `value` supports generic iteration like other container classes.  
  
I prefer each iteration to return a key-value pair of a JSON Pointer and value reference but I would like to ask your opinion regarding it and whether we can utilize the already implemented mechanisms/structures in the library.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-01-08 10:06:54 UTC  
> Updated at: 2024-01-08 10:07:24 UTC  
> Url: https://github.com/boostorg/json/issues/970#issuecomment-1880700850  

This could be implemented in an example first. What is the motivating use-case?

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-01-08 11:28:54 UTC  
> Url: https://github.com/boostorg/json/issues/970#issuecomment-1880825280  

Why specifically depth-first? Why not breadth-first? What's the use case? I agree with Vinnie, this sounds like an example.

---

## Comment 3

> Username: doganulus  
> Created at: 2024-01-08 14:22:12 UTC  
> Updated at: 2024-01-08 14:24:52 UTC  
> Url: https://github.com/boostorg/json/issues/970#issuecomment-1881105983  

We use JSON values as the configuration object for programs. Sometimes, it is nice to list all the hierarchical key-value pairs flatly. I agree that this could be a good `flatten` example.   
  
```  
"/a/b/c": 12,  
"/a/b/": "XYZ",  
"/a/d": true,  
"/a/e/c": 23,  
"/a/f/c": 44,  
```  
  
But more importantly, iteration support would make it easier to use `std::algorithm` over JSON values. For example, I want to select all keys that satisfy a certain pattern such as `/a/*/c`, and filter/apply an operation on them. In this case, I will easily iterate over the JSON value and check whether the key matches the expression.  
  
Depth-first is the most natural choice for me. I know there are multiple options to drive the iteration but one must be the default.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-01-08 22:49:18 UTC  
> Url: https://github.com/boostorg/json/issues/970#issuecomment-1881944176  

@doganulus I think you should write it as an example contribution to Boost.JSON

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-01-12 10:55:39 UTC  
> Url: https://github.com/boostorg/json/issues/970#issuecomment-1888878139  

Ok, so I _am_ interested in this in a form of an example. I'm not exactly sold on this being general-purpose enough to be added to the library proper. BTW, have you thought about integration with Boost.Graph?
