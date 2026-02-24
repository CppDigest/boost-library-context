# #1168 Add if constexpr macro and replace condition macro. [Merged]

> Username: awulkiew  
> Created at: 2023-07-01 19:08:04 UTC  
> Updated at: 2023-07-05 07:21:26 UTC  
> Merged at: 2023-07-04 09:36:33 UTC  
> Closed at: 2023-07-04 09:36:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1168  

This is a proposal adding conditional support of C++17 `if constexpr`. I modified only several files for now so we can start a discussion. If we have an agreement I'll modify other places.  
  
My intention was to have a solution as close as possible to the original language feature and to keep `if` keyword so editors can highlight it as usual, i.e.:  
```  
if constexpr (static_condition)  
```  
vs  
```  
if BOOST_GEOMETRY_CONSTEXPR (static_condition)  
```  
  
  
It's a followup of https://github.com/boostorg/geometry/pull/1160

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-07-02 08:59:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1168#pullrequestreview-1509510963  

I agree with this approach, thank you!

📁 include/boost/geometry/util/constexpr.hpp

```diff
  23 |+ 
  24 |+ #endif
  25 |+ 
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:56:35 UTC  
> Updated_at: 2023-07-02 08:59:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1168#discussion_r1249367014  

:+1:


📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 971 |     collection.get_turns();
 972 |-     if (BOOST_GEOMETRY_CONDITION(areal))
 972 |+     if BOOST_GEOMETRY_CONSTEXPR (areal)
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:57:06 UTC  
> Updated_at: 2023-07-02 08:59:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1168#discussion_r1249367518  

I like it

---

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
1017 |+             collection.discard_nonintersecting_deflated_rings();
1018 |+         }
1019 |     }
```

> Username: barendgehrels  
> Created_at: 2023-07-02 08:57:40 UTC  
> Updated_at: 2023-07-02 08:59:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1168#discussion_r1249368205  

Looks better than the original code


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2023-07-04 09:36:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1168#pullrequestreview-1512495492  

Thanks! Looks great!

---

## Comment 3

> Username: awulkiew  
> Created_at: 2023-07-04 18:09:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1168#issuecomment-1620594823  

@vissarion Thanks! This wasn't finished though. I'll create another PR with the rest. :)

---

## Comment 4

> Username: vissarion  
> Created_at: 2023-07-05 07:21:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1168#issuecomment-1621176413  

> @vissarion Thanks! This wasn't finished though. I'll create another PR with the rest. :)  
  
I know. I just merged it since there were the 1.83 and the enhancement labels and the beta is closing today. I guess there is no problem to have the rest of the support in 1.84.

---
