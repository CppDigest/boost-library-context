# #27 Add CMake tests [Closed]

> Username: pdimov  
> Created at: 2019-12-30 23:25:12 UTC  
> Updated at: 2020-04-29 15:51:44 UTC  
> Closed at: 2020-04-29 15:50:18 UTC  
> Url: https://github.com/boostorg/atomic/pull/27  



---

## Comment 1

> Username: Lastique  
> Created_at: 2019-12-31 16:29:44 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#issuecomment-569954661  

I don't really think a build system is something worth testing.

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-01-01 18:58:49 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#issuecomment-570075376  

Well, maybe. On the other hand, everything that isn't being tested, breaks earlier or later. Most maintainers even demand tests as part of the CMake pull requests, and rightly so.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-01-01 21:50:32 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#issuecomment-570086295  

I want to minimize maintenance effort spent on CMake. I'm not happy about having two build systems in Boost.Atomic (and other libraries), and so long as we're using Boost.Build as the main build system, I'd like to hear about CMake as little as possible.

---

## Comment 4

> Username: pdimov  
> Created_at: 2020-01-01 22:07:19 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#issuecomment-570087359  

Sure, it's your call how to minimize that. Other maintainers (I presume) prefer to, as long as they carry around the CMakeLists.txt file, be sure it works.  
  
But for Atomic specifically, there's another factor in play. There's value in having a model compiled library, one to which we can point people who want to add CMake support to their libraries. For header-only, we can use Assert (f.ex.) as a showcase; for compiled, for reasons already outlined, Atomic is our only choice at the moment.

---

## Review 5 [Commented]

> Username: Lastique  
> Created_at: 2020-01-02 21:11:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/27#pullrequestreview-337858618  

📁 .travis.yml

```diff
 380 |+       script:
 381 |+         - mkdir __build__ && cd __build__
 382 |+         - cmake -DBOOST_ENABLE_CMAKE=1 -DBoost_VERBOSE=1 -DBOOST_INCLUDE_LIBRARIES="atomic;assert;config;static_assert;type_traits" -DCMAKE_INSTALL_PREFIX=~/.local ..
```

> Username: Lastique  
> Created_at: 2020-01-02 21:06:32 UTC  
> Updated_at: 2020-01-02 21:11:57 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#discussion_r362629911  

I don't like that there is a list of dependencies. Also, install prefix of `~/.local` does not feel right.


📁 CMakeLists.txt

```diff
  41 |+ if(BUILD_TESTING)
  42 |+ 
  43 |+   add_subdirectory(test)
```

> Username: Lastique  
> Created_at: 2020-01-02 21:07:30 UTC  
> Updated_at: 2020-01-02 21:11:57 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#discussion_r362630178  

I don't like that the tests are part of the library CMakeLists.txt.


---

## Comment 6

> Username: Lastique  
> Created_at: 2020-01-02 21:22:27 UTC  
> Updated_at: 2020-01-02 21:22:59 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#issuecomment-570354972  

> Sure, it's your call how to minimize that. Other maintainers (I presume) prefer to, as long as they carry around the CMakeLists.txt file, be sure it works.  
  
Well, I don't really care about CMakeLists.txt, and I don't want to care. If someone uses it to build the library, that is their choice. I care about the official build system, which is Boost.Build.

---

## Comment 7

> Username: Lastique  
> Created_at: 2020-04-29 15:50:18 UTC  
> Url: https://github.com/boostorg/atomic/pull/27#issuecomment-621299832  

Given that the tests cannot be run anyway because Boost.Thread does not support CMake, and CMakeLists.txt is already tested, I don't think I will be merging this for now.

---
