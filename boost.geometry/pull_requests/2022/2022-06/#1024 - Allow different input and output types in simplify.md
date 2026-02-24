# #1024 Allow different input and output types in simplify [Merged]

> Username: awulkiew  
> Created at: 2022-06-24 22:41:27 UTC  
> Updated at: 2022-06-29 20:54:34 UTC  
> Merged at: 2022-06-29 20:54:34 UTC  
> Closed at: 2022-06-29 20:54:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1024  

Requested in this comment: https://github.com/boostorg/geometry/commit/0f72af95f0ac94f94022c6e4b7831895c2291dde#commitcomment-76745077

---

## Comment 1

> Username: icehuli  
> Created_at: 2022-06-26 10:17:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1024#issuecomment-1166482050  

Hi @awulkiew,  
  
I'm sorry, I think it is my fault that I didn't specified the problem more clearly. In the latest version of `buffer_inserter` `simplify_range` is used instead of `simplify`. `simplify_range` can produce a different output Type.  
https://github.com/boostorg/geometry/blob/6d57aedf7f2b883ed1ac0ffdc59d27c9342d3637/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp#L73  
  
So what needs to be changed is `simplify_input`:  
https://github.com/boostorg/geometry/blob/6d57aedf7f2b883ed1ac0ffdc59d27c9342d3637/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp#L58  
  
I'll try to create a PR for this.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2022-06-26 18:57:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1024#issuecomment-1166616038  

@icehuli No, it's ok. I stated that `simplify` should be able to take different input and output types and this PR is about that. This is more or less unrelated to `buffer`.  
  
Regarding `buffer`, I wanted to wait for @barendgehrels to give his opinion but you could create a PR addressing it so the discussion would be moved there.

---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-29 09:21:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1024#pullrequestreview-1023015553  

I understood that the reported problem was inside buffer itself, but this PR on itself looks also useful to me. Thanks!

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 664 |+         >::type;
 665 |+ 
 666 |+     BOOST_GEOMETRY_STATIC_ASSERT(
```

> Username: barendgehrels  
> Created_at: 2022-06-29 09:19:35 UTC  
> Updated_at: 2022-06-29 09:21:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1024#discussion_r909399929  

We keep using this? Or would new code use just `static_assert` ?

> Username: awulkiew  
> Created_at: 2022-06-29 19:36:18 UTC  
> Updated_at: 2022-06-29 19:37:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1024#discussion_r910331268  

For consistency and also because this macro has variadic number of arguments [1] so you can pass types after condition and message and these types will typically be displayed in the error message. Furthermore if you pass `false` as condition it will compile as long as a struct inside is not instantiated while `static_assert` will never compile. This is why we can't use `static_assert(false)` inside `not_implemented`.  
  
[1] https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/core/static_assert.hpp#L24


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2022-06-29 09:59:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1024#pullrequestreview-1023072478  

Thanks!

---
