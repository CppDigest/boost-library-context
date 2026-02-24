# #973 Add free function for calculation of simple continued fraction coefficients [Closed]

> Username: mborland  
> Created at: 2023-04-04 13:17:14 UTC  
> Updated at: 2024-01-24 21:33:48 UTC  
> Closed at: 2023-04-13 12:11:46 UTC  
> Url: https://github.com/boostorg/math/pull/973  

I think this solves #971 while keeping the class immutable like @NAThompson wants. Since this header requires C++17 guaranteed copy elision should make passing the data from the class to the user through the free function inexpensive. @Tomaqa I used your use case as the additional test case.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2023-04-04 15:18:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/973#pullrequestreview-1371234803  

📁 doc/internals/simple_continued_fraction.qbk

```diff
  23 |+         const std::vector<Z>& partial_denominators() const;
  24 |+ 
  25 |+         inline std::vector<Z>&& get_data() noexcept;
```

> Username: NAThompson  
> Created_at: 2023-04-04 15:18:45 UTC  
> Url: https://github.com/boostorg/math/pull/973#discussion_r1157415340  

Is `get_data()` the standard nomenclature for move return?

> Username: mborland  
> Created_at: 2023-04-04 15:25:43 UTC  
> Url: https://github.com/boostorg/math/pull/973#discussion_r1157424598  

I've seen it elsewhere before.


---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2023-04-04 15:20:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/973#pullrequestreview-1371238585  

📁 include/boost/math/tools/simple_continued_fraction.hpp

```diff
 184 |+ {
 185 |+     auto temp = simple_continued_fraction(x);
 186 |+     return temp.get_data();
```

> Username: NAThompson  
> Created_at: 2023-04-04 15:20:38 UTC  
> Updated_at: 2023-04-04 15:20:39 UTC  
> Url: https://github.com/boostorg/math/pull/973#discussion_r1157417850  

The reverse might make more sense?  
  
Namely, the free function should do the calculation, and the class should consume the vector.  
  
Not that it matters much in this case . . .

> Username: mborland  
> Created_at: 2023-04-04 15:30:03 UTC  
> Url: https://github.com/boostorg/math/pull/973#discussion_r1157430360  

This way avoids breaking any existing code because that would change the constructor.


---

## Comment 3

> Username: mborland  
> Created_at: 2023-04-06 12:10:37 UTC  
> Url: https://github.com/boostorg/math/pull/973#issuecomment-1498962169  

The CI failure is failed to clone.

---

## Comment 4

> Username: Tomaqa  
> Created_at: 2023-04-11 13:28:42 UTC  
> Url: https://github.com/boostorg/math/pull/973#issuecomment-1503354304  

It is a way.  
However, I need to convert the resulting coefficients back to a rational or floating-point number.  
It can again be achieved by a free function which operates upon a vector of coefficients, but I think it is not conceptual. I just want to convert the resulting `simple_continued_fraction` to `rational`.  
  
A possible way is to add constructor `simple_continued_fraction(std::vector<> coefficients)` where the coefficients will be checked whether they are valid, which should be easy to implement and inexpensive to run.  
Also, `std::move` can be used.  
  
If this idea is fine with you, I can implement it, but I will need to use your commits.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-04-11 13:46:16 UTC  
> Url: https://github.com/boostorg/math/pull/973#issuecomment-1503386341  

> If this idea is fine with you, I can implement it, but I will need to use your commits.  
  
Go ahead. This is on a branch in the boost.math tree so you will be able to clone it.

---
