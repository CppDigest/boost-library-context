# #112 Add missing includes [Merged]

> Username: ashtum  
> Created at: 2023-11-15 14:22:46 UTC  
> Updated at: 2023-12-03 23:19:00 UTC  
> Merged at: 2023-12-03 23:19:00 UTC  
> Closed at: 2023-12-03 23:19:00 UTC  
> Url: https://github.com/boostorg/property_tree/pull/112  



---

## Review 1 [Commented]

> Username: ashtum  
> Created_at: 2023-11-15 14:45:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/property_tree/pull/112#pullrequestreview-1732202336  

📁 include/boost/property_tree/json_parser/detail/parser.hpp

```diff
   6 | #include <boost/core/ref.hpp>
   7 | #include <boost/bind/bind.hpp>
   8 |+ #include <boost/bind/placeholders.hpp>
```

> Username: ashtum  
> Created_at: 2023-11-15 14:45:57 UTC  
> Updated_at: 2023-11-15 14:58:19 UTC  
> Url: https://github.com/boostorg/property_tree/pull/112#discussion_r1394308546  

@pdimov, I initially added this header to address https://github.com/boostorg/property_tree/pull/101, but it looked incorrect on second thought. I believe it should be the user's responsibility to manage the definition of `BOOST_BIND_NO_PLACEHOLDERS` and it has nothing to do with including `boost/bind/placeholders.hpp`. what do you think?


---

## Comment 2

> Username: pdimov  
> Created_at: 2023-11-15 16:30:52 UTC  
> Url: https://github.com/boostorg/property_tree/pull/112#issuecomment-1812867565  

It's hard to say what's the history here.  
  
I suppose this dates from the days when including `<boost/bind.hpp>` made `_1` et al available in the global namespace, which later conflicted with `using namespace std::placeholders;` which also made `_1` available.  
  
So I assume people who were defining `BOOST_BIND_NO_PLACEHOLDERS` did that in order to be able to use `using namespace std::placeholders;` in their code. But doing so broke `parser.hpp`.  
  
This problem will not occur today, because `<boost/bind/bind.hpp>` leaves the placeholders defined in `namespace boost::placeholders`, avoiding the conflict. But for backward compatibility, we may want to still support the configuration with `BOOST_BIND_NO_PLACEHOLDERS` defined.  
  
So adding this include is probably correct, but we need a test that shows why.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-11-15 16:34:53 UTC  
> Url: https://github.com/boostorg/property_tree/pull/112#issuecomment-1812874387  

As a general rule, every change that does not immediately fix a test failure needs to follow the procedure of: first commit adds a test that fails without the change, second commit adds the change that fixes the test failure.

---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-11-16 12:21:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/property_tree/pull/112#pullrequestreview-1734243994  

📁 test/Jamfile.v2

```diff
  21 | run test_json_parser.cpp ;
  22 | run test_json_parser2.cpp ;
  23 |+ run test_json_parser3.cpp ;
```

> Username: pdimov  
> Created_at: 2023-11-16 12:21:49 UTC  
> Url: https://github.com/boostorg/property_tree/pull/112#discussion_r1395613831  

This should probably be `compile` instead of `run`, as we aren't testing anything besides compilation.


---

## Comment 5

> Username: pdimov  
> Created_at: 2023-11-16 12:23:50 UTC  
> Url: https://github.com/boostorg/property_tree/pull/112#issuecomment-1814340924  

Would you please be able to extract the new test into a separate PR? I'd like to merge the original contribution (#101) as a form of giving credit on top of it.

---

## Comment 6

> Username: ashtum  
> Created_at: 2023-11-16 12:54:32 UTC  
> Url: https://github.com/boostorg/property_tree/pull/112#issuecomment-1814384451  

Then this PR is waiting for https://github.com/boostorg/property_tree/issues/100 response.

---
