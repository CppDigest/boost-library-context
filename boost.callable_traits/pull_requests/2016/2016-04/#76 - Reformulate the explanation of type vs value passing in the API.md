# #76 Reformulate the explanation of type vs value passing in the API [Merged]

> Username: ldionne  
> Created at: 2016-04-19 18:44:31 UTC  
> Updated at: 2016-04-19 20:59:40 UTC  
> Merged at: 2016-04-19 20:57:05 UTC  
> Closed at: 2016-04-19 20:57:05 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/76  

I thought that not everybody would get the "_which often eliminates the need for decltype_" part. It's not merely a reformulation, it also brings the rationale from a slightly different angle ("when you don't have an object at hand, etc.."). But if this is not **what** you want to say, disregard my suggestion.

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-04-19 18:50:47 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/76#issuecomment-212066296  

Or you could put it the other way around, and pretend that you're taking types by default, and also accept values when it makes sense. That might be more consistent with your choice to then use the type syntax only in the rest of the intro file.

---

## Comment 2

> Username: badair  
> Created_at: 2016-04-19 20:59:40 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/76#issuecomment-212126870  

Thanks! I like your wording much better.

---
