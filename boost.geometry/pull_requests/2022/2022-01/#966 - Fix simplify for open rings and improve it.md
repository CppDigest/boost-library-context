# #966 Fix simplify for open rings and improve it [Merged]

> Username: awulkiew  
> Created at: 2022-01-18 16:28:45 UTC  
> Updated at: 2022-04-05 20:19:05 UTC  
> Merged at: 2022-01-19 23:37:41 UTC  
> Closed at: 2022-01-19 23:37:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/966  

Fix for https://github.com/boostorg/geometry/issues/956  
  
Furthermore:  
- avoid duplicating closing point for closed rings while rotating the range  
- use more efficient strategy while calculating index of opposite point

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-01-19 08:13:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/966#pullrequestreview-856432680  

Thanks, I am OK with changing this.

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2022-01-19 09:32:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/966#pullrequestreview-856516579  

Still a question but the tests look good, I so it will work.  
Maybe a small extra comment would be useful.

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 482 |+                 --rot_end;
 483 |+                 if (rot_index == size - 1) { rot_index = 0; }
 484 |+             }
```

> Username: barendgehrels  
> Created_at: 2022-01-19 09:28:40 UTC  
> Updated_at: 2022-01-19 09:32:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/966#discussion_r787534524  

Right, thanks!

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 496 |+             {
 497 |+                 range::pop_back(out);
 498 |+             }
```

> Username: barendgehrels  
> Created_at: 2022-01-19 09:31:29 UTC  
> Updated_at: 2022-01-19 09:32:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/966#discussion_r787537658  

Maybe I get it wrongly, but above you rotate with one point less (fine) - and now you pop another one. Is that not one too much?

> Username: awulkiew  
> Created_at: 2022-01-19 10:52:06 UTC  
> Updated_at: 2022-01-19 10:52:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/966#discussion_r787625629  

These are two different issues.  
  
The point above is removed only for closed rings. Otherwise when they are rotated then two equal points (beginning and closing) are moved somewhere into the middle of the ring.  
  
After the rotation an additional closing point is pushed back, always, even for open rings. This is why here, for open rings this additional point is removed.

> Username: awulkiew  
> Created_at: 2022-01-19 10:53:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/966#discussion_r787627238  

An alternative to `pop_back()` would be not copying the last point for open output ring inside the `simplify_range<0>::apply()`.


---
