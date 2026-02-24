# #1075 basic implementation of context-based path traction [Open]

> Username: grisumbras  
> Created at: 2025-02-25 02:13:05 UTC  
> Updated at: 2026-02-10 11:01:23 UTC  
> Url: https://github.com/boostorg/json/pull/1075  

Very rough draft of the idea.  
  
For a context type Ctx the user is supposed to implement these functions:  
```  
void tag_invoke(context_key_push_tag, string_view,  Ctx const&);  
void tag_invoke(context_key_pop_tag, Ctx const&);  
void tag_invoke(context_index_push_tag, std::size_t, Ctx const&);  
void tag_invoke(context_index_pop_tag, Ctx const&);  
```  
  
The library would invoke them when traversing through the converted/parsed/serialized value.  
  
Things to think about:  
  
* The user may actually want to set `error_code` in those callbacks.  
* The user may want to clear `error_code` in those functions. But then where would we get the results the calling function was supposed to create?

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-02-25 02:23:26 UTC  
> Url: https://github.com/boostorg/json/pull/1075#issuecomment-2680223349  

An automated preview of the documentation is available at [https://1075.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1075.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-02-25 03:28:08 UTC  
> Url: https://github.com/boostorg/json/pull/1075#issuecomment-2680332008  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1075/pullrequest-condensed-639003e.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1075/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1075/pullrequest.html)

---

## Comment 3

> Username: RoyBellingan  
> Created_at: 2025-06-23 02:38:58 UTC  
> Url: https://github.com/boostorg/json/pull/1075#issuecomment-2994727463  

Is very interesting and clean.  
I would suggest in this section  
  
https://github.com/grisumbras/json/blob/e4e192a84f7482aaad923575e34e6f37669252d0/include/boost/json/detail/value_to.hpp#L376  
  
To move the   
  json::context_key_push(ctx, D::name);  
    
Before the check, so we can capture the "last processed" json pointer

---

## Comment 4

> Username: sdebionne  
> Created_at: 2026-02-10 11:00:16 UTC  
> Url: https://github.com/boostorg/json/pull/1075#issuecomment-3876904027  

This MR fulfills my needs -thank you for that- and I would be glad if it get merged at some point.  
  
But the CI fails for compilers other than GCC because it handles C++ variadic function in a different way. Other compilers treat them as C variadic function and complain that it `cannot pass object of non-trivial type through variadic function`.   
  
Is there any way around that? Was this just a convenient way of forward declaring the `tag_invoke` customization points for the new tags?

---

## Review 5 [Commented]

> Username: sdebionne  
> Created_at: 2026-02-10 11:01:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/1075#pullrequestreview-3778270536  

📁 include/boost/json/impl/conversion.hpp

```diff
 586 |+ tag_invoke(context_index_pop_tag, ...)
 587 |+ {
 588 |+ }
```

> Username: sdebionne  
> Created_at: 2026-02-10 11:01:23 UTC  
> Url: https://github.com/boostorg/json/pull/1075#discussion_r2787287680  

I was referring to these lines in the above comment.


---
