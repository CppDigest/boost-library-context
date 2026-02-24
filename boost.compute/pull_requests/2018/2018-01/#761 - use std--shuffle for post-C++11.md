# #761 use std::shuffle for post-C++11 [Merged]

> Username: jeffhammond  
> Created at: 2018-01-22 16:07:01 UTC  
> Updated at: 2018-03-01 13:45:10 UTC  
> Merged at: 2018-02-28 08:56:48 UTC  
> Closed at: 2018-02-28 08:56:48 UTC  
> Url: https://github.com/boostorg/compute/pull/761  

`std::random_shuffle` was deprecated in C++14 and deleted in C++17.  `std::shuffle` was added in C++11.  using it fixes build issues with clang+libc++ when compiling with `-std=c++1z`.  
  
fixes issue #760  
  
This is my first attempt at contributing.  
  
I do not consider this trivial change to be worthy of copyright, but I work for Intel and will get the open-source contribution paperwork approved ASAP.

---

## Comment 1

> Username: jeffhammond  
> Created_at: 2018-01-22 20:02:06 UTC  
> Updated_at: 2018-01-22 22:05:18 UTC  
> Url: https://github.com/boostorg/compute/pull/761#issuecomment-359547175  

I have Intel approval to contribute.  ~If it is appropriate to add copyright for a trivial change, then I'll do that.~  
  
Update: I confirmed that Intel does not require me to attach copyright on minor changes like this.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2018-01-22 20:25:36 UTC  
> Url: https://github.com/boostorg/compute/pull/761#issuecomment-359553563  

>  If it is appropriate to add copyright for a trivial change, then I'll do that.  
  
It depends on you. Usually, you would add copyright on a bigger change, but that's not a rule.

---

## Review 3 [Commented]

> Username: jszuppe  
> Created_at: 2018-01-22 20:38:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/761#pullrequestreview-90617663  

📁 include/boost/compute/algorithm/random_shuffle.hpp

```diff
  52 |     boost::iota(random_indices, 0);
  53 |+ #ifdef BOOST_COMPUTE_USE_CPP11
  54 |+     std::default_random_engine random_engine;
```

> Username: jszuppe  
> Created_at: 2018-01-22 20:38:24 UTC  
> Updated_at: 2018-02-27 18:46:15 UTC  
> Url: https://github.com/boostorg/compute/pull/761#discussion_r163061320  

Would it be better to seed the random engine with `std::random_device`?

> Username: jeffhammond  
> Created_at: 2018-01-22 20:44:33 UTC  
> Updated_at: 2018-02-27 18:46:15 UTC  
> Url: https://github.com/boostorg/compute/pull/761#discussion_r163062585  

Yes.  Will fix.


---

## Review 4 [Changes requested]

> Username: jszuppe  
> Created_at: 2018-01-22 21:09:36 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/compute/pull/761#pullrequestreview-90626839  

📁 include/boost/compute/algorithm/random_shuffle.hpp

```diff
  53 |+ #ifdef BOOST_COMPUTE_USE_CPP11
  54 |+     std::random_device nondeterministic_randomness;
  55 |+     std::default_random_engine random_engine(nondeterministic_randomness);
```

> Username: jszuppe  
> Created_at: 2018-01-22 21:09:26 UTC  
> Updated_at: 2018-02-27 18:46:15 UTC  
> Url: https://github.com/boostorg/compute/pull/761#discussion_r163068879  

Shouldn't it be  
```  
std::default_random_engine random_engine(nondeterministic_randomness());  
```  
?


---

## Comment 5

> Username: jszuppe  
> Created_at: 2018-02-27 18:47:24 UTC  
> Url: https://github.com/boostorg/compute/pull/761#issuecomment-368984022  

Rebased on `develop`. I'll merge after Travis-CI confirm everything works.

---
