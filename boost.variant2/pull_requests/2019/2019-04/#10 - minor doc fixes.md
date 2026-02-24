# #10 minor doc fixes [Closed]

> Username: akrzemi1  
> Created at: 2019-04-07 00:01:28 UTC  
> Updated at: 2019-05-12 16:20:36 UTC  
> Closed at: 2019-05-12 16:20:36 UTC  
> Url: https://github.com/boostorg/variant2/pull/10  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2019-04-07 03:04:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant2/pull/10#pullrequestreview-223563515  

📁 doc/variant2/reference.adoc

```diff
 729 | +
 728 |- Effects: :: If `v.index()` is `I`, returns a reference to the object stored in
 730 |+ Requires: :: `I < sizeof...(T)`. Otherwise, the program is ill-formed.
```

> Username: pdimov  
> Created_at: 2019-04-07 03:04:30 UTC  
> Url: https://github.com/boostorg/variant2/pull/10#discussion_r272815975  

This one isn't true, I think.

> Username: akrzemi1  
> Created_at: 2019-04-07 11:10:56 UTC  
> Url: https://github.com/boostorg/variant2/pull/10#discussion_r272829468  

In your implementation it (rightly) causes a compiler error. This is also what std::variant does: http://eel.is/c++draft/variant.get#3

> Username: pdimov  
> Created_at: 2019-04-07 11:31:06 UTC  
> Updated_at: 2019-04-07 11:31:07 UTC  
> Url: https://github.com/boostorg/variant2/pull/10#discussion_r272830192  

It causes a substitution failure in `variant_alternative_t`. This is equivalent to a compile-time error when there are no other overloads, but not always.  
  
Of course the documentation of `get_if` disagrees so I need to fix that instead.

> Username: akrzemi1  
> Created_at: 2019-04-08 07:17:57 UTC  
> Updated_at: 2019-04-08 07:17:58 UTC  
> Url: https://github.com/boostorg/variant2/pull/10#discussion_r272911086  

Given that `I` is known statically and that the `sizeof...(T)` is known statically, there shouldn't be any problem with statically rejecting code based on condition `I < sizeof...(T)` in all cases. Or am I missing something?

> Username: pdimov  
> Created_at: 2019-04-09 14:51:20 UTC  
> Url: https://github.com/boostorg/variant2/pull/10#discussion_r273532493  

As it happens I already have a `static_assert` inside `get`, but it will never fire. When `I >= sizeof...(T)` the substitution failure in `variant_alternative_t` causes `get` to drop out of overload resolution, so it can never be called and the `static_assert` can never activate.

> Username: akrzemi1  
> Created_at: 2019-04-10 08:34:23 UTC  
> Url: https://github.com/boostorg/variant2/pull/10#discussion_r273846145  

Thanks. In that case, maybe:  
  
```  
Remarks: :: This function does not participate in overload resolution unless `I < sizeof...(T)` is `true`.  
```


---
