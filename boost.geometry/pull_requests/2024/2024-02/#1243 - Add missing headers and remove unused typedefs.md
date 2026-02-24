# #1243 Add missing headers and remove unused typedefs [Closed]

> Username: vissarion  
> Created at: 2024-02-13 11:36:44 UTC  
> Updated at: 2024-04-01 11:29:29 UTC  
> Closed at: 2024-02-26 10:13:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1243  

fixes failing CI header tests (and removes some unused typedefs appeared as warnings)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-24 21:45:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1243#pullrequestreview-1899571680  

📁 include/boost/geometry/algorithms/detail/within/within_no_turns.hpp

```diff
  19 | #define BOOST_GEOMETRY_ALGORITHMS_DETAIL_WITHIN_WITHIN_NO_TURNS_HPP
  20 | 
  21 |+ #include <boost/type_traits/is_base_of.hpp>
```

> Username: barendgehrels  
> Created_at: 2024-02-24 21:45:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1243#discussion_r1501702495  

Sorry, I had not seen this PR before.  
And I tackled the same problem because I got a mail that my merge was red.  
  
I think this  line is not the right fix - we use it from `std` now (in several other places)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-24 21:47:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1243#pullrequestreview-1899571825  

📁 include/boost/geometry/util/rational.hpp

```diff
  17 | #include <boost/rational.hpp>
  18 | #include <boost/numeric/conversion/bounds.hpp>
  19 |+ #include <boost/numeric/conversion/converter.hpp>
```

> Username: barendgehrels  
> Created_at: 2024-02-24 21:47:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1243#discussion_r1501702717  

So indeed this fixed it.  
I fixed it in another way (again, because I didn't notice this earlier, sorry) and I think it is a better fix. See #1246


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-02-24 21:49:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1243#issuecomment-1962739622  

I see this PR is already 2 weeks old. My bad for not reviewing of course.  
But I think a CI fix should get some priority - feel free to poke such a thing, or even merge unreviewed

---

## Comment 4

> Username: vissarion  
> Created_at: 2024-02-26 10:13:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1243#issuecomment-1963758705  

Closing as duplicate of https://github.com/boostorg/geometry/pull/1246

---
