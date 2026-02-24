# #280 Add user callback to `vf2_subgraph_mono` to potentially stop search before each check. [Open]

> Username: cqc-alec  
> Created at: 2021-12-14 10:40:32 UTC  
> Updated at: 2024-11-19 12:58:01 UTC  
> Url: https://github.com/boostorg/graph/pull/280  

Also update documentation and add tests.  
  
See the discussion on #271 .  
  
Remarks:  
  
* I used a simple function pointer for the type rather than a templated "callable" so as to be able to default it, thereby maintaining compatibility with existing code. An alternative would be to define a new method or methods with a different name; this seems a bit like overkill but I can do that if preferred.  
* I only changed `vf2_subgraph_mono`, not `vf2_subgraph_iso`, but it should be simple to do the same thing there too if desired.  
* The tests are pretty basic. It isn't obvious to me how to test this fully without knowing internal details of the order of search. Suggestions welcome.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2022-07-22 00:12:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/280#pullrequestreview-1047255813  

📁 doc/vf2_sub_graph_iso.html

```diff
  47 |-                       SubGraphIsoMapCallback user_callback)
  47 |+                       SubGraphIsoMapCallback user_callback,
  48 |+                       bool(*user_step_callback)() = &vf2_trivial_step_callback)
```

> Username: jeremy-murphy  
> Created_at: 2022-07-22 00:12:24 UTC  
> Url: https://github.com/boostorg/graph/pull/280#discussion_r927191252  

Instead of being a concrete function pointer, this should be a template like most other parameters. That way, users can pass in whatever they like: lambda, function object, etc.


---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2022-07-22 00:15:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/280#pullrequestreview-1047257227  

📁 include/boost/graph/vf2_sub_graph_iso.hpp

```diff
 780 | 
 781 |     recur:
 782 |+         if (!user_step_callback()) {
```

> Username: jeremy-murphy  
> Created_at: 2022-07-22 00:15:41 UTC  
> Updated_at: 2022-07-22 00:15:42 UTC  
> Url: https://github.com/boostorg/graph/pull/280#discussion_r927192338  

I think this callback should take in whatever data might change between each call to it. Even though in your case you'll ignore it, someone else might have a reason to examine the internals of whatever state is changing. I guess it's just that `s` object?


---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2022-07-22 00:16:24 UTC  
> Url: https://github.com/boostorg/graph/pull/280#issuecomment-1192050766  

Looks great so far! Thanks for keeping it simple, sorry it took me so long to take a look. I just made a couple of requests for changes.

---

## Comment 4

> Username: cqc-alec  
> Created_at: 2022-07-23 12:34:22 UTC  
> Url: https://github.com/boostorg/graph/pull/280#issuecomment-1193118345  

Thank you for the review Jeremy! I have actually switched to a different solution now, so no longer need this. I may come back to it anyway if you or others think it generally useful, though I cannot prioritize it just now. So, feel free to close, complete or leave open this PR as you see fit.  
  
By the way, the reason I didn't use a template type for the callback was that I couldn't see how to do so while maintaining backward compatibility (i.e. how to set a no-op default value). I guess an alternative would be to overload the whole function.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2022-07-24 23:35:15 UTC  
> Url: https://github.com/boostorg/graph/pull/280#issuecomment-1193418106  

OK, no worries. I'll leave this open for a while in case someone else needs the same functionality and wants to finish it off.

---

## Comment 6

> Username: cqc-alec  
> Created_at: 2024-11-19 09:27:59 UTC  
> Url: https://github.com/boostorg/graph/pull/280#issuecomment-2485153204  

I wonder if it's time to close this now @jeremy-murphy ?

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2024-11-19 12:49:53 UTC  
> Url: https://github.com/boostorg/graph/pull/280#issuecomment-2485630026  

> I wonder if it's time to close this now @jeremy-murphy ?  
  
I still think it's a good idea, so I'll finish it off one day if you don't.  :)

---

## Comment 8

> Username: cqc-alec  
> Created_at: 2024-11-19 12:58:00 UTC  
> Url: https://github.com/boostorg/graph/pull/280#issuecomment-2485648296  

> > I wonder if it's time to close this now @jeremy-murphy ?  
>   
> I still think it's a good idea, so I'll finish it off one day if you don't. :)  
  
OK!

---
