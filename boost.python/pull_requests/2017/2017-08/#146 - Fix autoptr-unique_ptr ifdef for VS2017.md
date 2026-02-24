# #146 Fix autoptr/unique_ptr ifdef for VS2017. [Merged]

> Username: garyfurnish  
> Created at: 2017-08-21 04:10:53 UTC  
> Updated at: 2017-10-25 12:07:19 UTC  
> Merged at: 2017-10-25 12:07:18 UTC  
> Closed at: 2017-10-25 12:07:19 UTC  
> Url: https://github.com/boostorg/python/pull/146  

This fixes autoptr detection on VS2017.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2017-08-21 12:39:27 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323733977  

Can you elaborate a bit on a) the rationale for this patch and b) the symptoms you are observing ?  
I'm not entirely convinced of the patch, at least not without further (inline) comments. The macro discriminates the availability of smart pointers but the conditional code involves `std::move`. There is no apparent relationship between the two (other than that both are C++11 features)...

---

## Comment 2

> Username: garyfurnish  
> Created_at: 2017-08-21 14:56:15 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323765404  

The rationale is that auto_ptr is permanently removed from MS stl as of VS2017 update 3 per c++ standard, so any auto_ptr use is an unrescuable problem.  You can't copy unique pointers so initialization must be done via move.  I suspect this subtly breaks other things.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2017-08-21 15:09:22 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323769369  

And VS2017 doesn't set `__cplusplus` properly ?

---

## Comment 4

> Username: garyfurnish  
> Created_at: 2017-08-21 15:16:42 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323771592  

No :(

---

## Comment 5

> Username: garyfurnish  
> Created_at: 2017-08-21 15:35:12 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323777206  

Interestingly enough in spite of the failing integration tests on VS this does compile successfully on my machine with VS 2017 Update 3, so maybe its something that needs a work around that was a silently fixed compiler bug.

---

## Comment 6

> Username: garyfurnish  
> Created_at: 2017-08-21 15:43:48 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323779645  

Is it possible to get more versions of VS on appveyor?

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2017-08-21 15:48:23 UTC  
> Updated_at: 2017-08-21 16:10:51 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323781009  

@garyfurnish : the problem only occurs when compiling in C++17 mode via `/std:c++latest` or `/std:c++17`, if you're talking about the official Boost regression test runners. Half of the regression test runners are using `/std:c++latest`, and these are failing.  
  
I believe support for `std::move` is checked via `BOOST_NO_CXX11_RVALUE_REFERENCES` most of the time, so maybe both could be used? Even though I don't know what C++11 smart pointers without rvalue references would mean, maybe only `std::shared_ptr` and `std::weak_ptr`...

---

## Comment 8

> Username: garyfurnish  
> Created_at: 2017-08-21 15:59:12 UTC  
> Updated_at: 2017-08-21 15:59:32 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323784078  

@MarcelRaad Well, there are really a few issues here.   
1) the scons that runs the tests on appveyor doesn't detect c++11 correctly on windows  
2) if you fix c++latest to use shared_ptr, there is an awful linker error on appveyor.  Somewhere between the version of vc++ on appveyor and 2017 update 3, the error mysteriously stopped being an error, so I'm assuming it was some sort of type system bug.  
3) Having fixed both 1 and 2 and running on 2017 update 3, some of the tests on windows are failing because there are other places where c++11 support detection on windows aren't detected correctly.  Will fix this after the eclipse.  
4) But fixing the regression errors on appveyor will probably require some sort of dicscrimination on MSVC version.  It would be nice if I knew the exact version where it started working, but I don't have an array of VS machines setup.  I'm thinking defining a BOOST_PYTHON_MSVC_WORKAROUND that when true defaults things to autoptr so as to not break existing code.    
  
But I think I know how to fix all of the current bugs on std:c++ latest.  I only have 3 tests not passing right now, and they seem straightforward detection errors.  Its just going to take some creativity to figure out how to deal with that VC bug.

---

## Comment 9

> Username: garyfurnish  
> Created_at: 2017-08-21 21:03:44 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-323852559  

This appears to work correctly and pass tests on MSVC 2017 update 3 on a real machine with scons beta 3.  I believe the appveyor errors are spurious and were there pre-modifications, so this should be almost good to go.  
There are errors on import.  This be a preexisting bug, so I think this is good to go!  
For whatever reason travis is hung up on running this, so I did test it on a linux box and it works fine just in case the somewhat obvious changes didn't work right.  
Does this need anything else to pass inspection?

---

## Review 10 [Changes requested]

> Username: glenfe  
> Created_at: 2017-08-26 18:09:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/python/pull/146#pullrequestreview-58806273  

📁 include/boost/python/to_python_indirect.hpp

```diff
  85 |           typedef boost::shared_ptr<T> smart_pointer;
  86 |- # elif __cplusplus < 201103L
  86 |+ # elif BOOST_NO_CXX11_SMART_PTR
```

> Username: glenfe  
> Created_at: 2017-08-26 18:09:36 UTC  
> Updated_at: 2017-08-26 18:26:59 UTC  
> Url: https://github.com/boostorg/python/pull/146#discussion_r135394084  

#elif defined(BOOST_NO_CXX11_SMART_PTR)


---

## Comment 11

> Username: garyfurnish  
> Created_at: 2017-09-01 02:16:03 UTC  
> Url: https://github.com/boostorg/python/pull/146#issuecomment-326469669  

Bump.

---
