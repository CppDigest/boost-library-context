# #3 Add const specifier to the operators == and != of the iterators. [Closed]

> Username: ptal  
> Created at: 2014-01-08 18:10:42 UTC  
> Updated at: 2014-08-15 17:56:19 UTC  
> Closed at: 2014-03-13 08:03:37 UTC  
> Url: https://github.com/boostorg/coroutine/pull/3  

Without the const specifier, these iterators cannot be encapsulated in other iterators (tested with those created with the boost iterator library).

---
