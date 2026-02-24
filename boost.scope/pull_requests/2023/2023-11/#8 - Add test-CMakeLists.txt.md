# #8 Add test/CMakeLists.txt [Merged]

> Username: pdimov  
> Created at: 2023-11-28 14:50:50 UTC  
> Updated at: 2023-11-28 16:59:22 UTC  
> Merged at: 2023-11-28 16:59:22 UTC  
> Closed at: 2023-11-28 16:59:22 UTC  
> Url: https://github.com/boostorg/scope/pull/8  



---

## Comment 1

> Username: Lastique  
> Created_at: 2023-11-28 15:13:36 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830047321  

Thanks.  
  
How does one test this?  
  
Should this be added to CI?

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-11-28 15:17:56 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830055879  

Also, I think it should enforce at least C++11 (but not override a higher level, if it is requested by user).

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-11-28 15:25:46 UTC  
> Updated_at: 2023-11-28 15:26:49 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830070822  

Enforcing C++11 is automatic because the tests link to Boost::scope, which should have the cxx_std_11 requirement.  
  
Edit: it currently doesn't because `boostdep --cmake` takes the C++ requirement from `meta/libraries.json`, and you don't have one yet.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-11-28 15:29:03 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830079152  

For CI, I prefer to have separate jobs for the CMake tests (and separate jobs for the subdir/install/test variations of these), so this is how I run the CMake tests in CI: https://github.com/boostorg/system/blob/8bc32b72674580a3d72af796a9cb2ef664bd7d53/.github/workflows/ci.yml#L406-L420  
  
But you could in principle just integrate these in your `Run CMake tests` step, if you prefer.

---

## Comment 5

> Username: Lastique  
> Created_at: 2023-11-28 16:08:42 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830168024  

> Enforcing C++11 is automatic because the tests link to Boost::scope, which should have the cxx_std_11 requirement.  
  
Which it didn't. I have now added a list of features the library requires. The tests require a few more that will have to be listed. I don't know how I would do this with `boost_test` as it doesn't expose the target name.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-11-28 16:19:18 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830217223  

> Which it didn't.  
  
Right. As I said in an edit to my comment,  
  
> Edit: it currently doesn't because boostdep --cmake takes the C++ requirement from meta/libraries.json, and you don't have one yet.  
  
CMake has moved away from the individual compile features and towards the "meta" features cxx_std_XX; while it supports the original `cxx_constexpr` et al, they are no longer adding them for C++14 and above. So I'd recommend just adding `meta/libraries.json` and rerunning `boostdep --cmake` instead.  
  
What do the tests require in addition to C++11? `boost_test` supports a `COMPILE_FEATURES` option, and the `BOOST_TEST_COMPILE_FEATURES` global variable.

---

## Comment 7

> Username: Lastique  
> Created_at: 2023-11-28 16:38:54 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830265671  

> CMake has moved away from the individual compile features and towards the "meta" features cxx_std_XX  
  
Well, the library should work on a reasonably good implementation of C++11, but not necessarily complete. I don't want to strictly require full conformance with C++11. Also I'm not sure how specifying `cxx_std_11` would interact with user specifying e.g. `CMAKE_CXX_STANDARD=17`.  
  
> What do the tests require in addition to C++11?  
  
Not in addition to C++11 but rather more C++11 features. See the [Jamfile](https://github.com/Lastique/scope/blob/5a0f2bc43cc887285aff5a7d58ad20e70835bb73/test/Jamfile#L19-L40), I would like to replicate that behavior.  
  
> `boost_test` supports a `COMPILE_FEATURES` option, and the `BOOST_TEST_COMPILE_FEATURES` global variable.  
  
I see. I think, that should work for C++ features.

---

## Comment 8

> Username: pdimov  
> Created_at: 2023-11-28 16:43:34 UTC  
> Url: https://github.com/boostorg/scope/pull/8#issuecomment-1830273670  

> Also I'm not sure how specifying `cxx_std_11` would interact with user specifying e.g. `CMAKE_CXX_STANDARD=17`.  
  
Doesn't affect it. `CMAKE_CXX_STANDARD` is a user variable for setting the C++ standard, while `cxx_std_11` is a library requirement. If `CMAKE_CXX_STANDARD` is higher than the required standard, nothing is changed. If `CMAKE_CXX_STANDARD` isn't set, the library and everything that links to it is compiled with `-std=c++11`.  
  
I'm not sure what happens when `CMAKE_CXX_STANDARD=03` is used in conjunction with `cxx_std_11`, but I'm not sure I care that much. :-)  
  
In short, CMake tries to do the "right thing" in all cases. Idiomatic is to use `cxx_std_11` for the library.

---
