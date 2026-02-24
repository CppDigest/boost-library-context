# #999 file_mode::write_new available only on C++17 or later [Closed]

> Username: chrisics  
> Created at: 2018-01-28 19:53:28 UTC  
> Updated at: 2019-02-08 03:09:03 UTC  
> Closed at: 2019-02-08 03:09:03 UTC  
> Url: https://github.com/boostorg/beast/pull/999  

Fixes issue https://github.com/boostorg/beast/issues/982  
  
Since we don't want to loose the "write_new" option to open files,   
and since this option is only available in std::fopen since c++17   
(apart from the posix and win32 api's)  
I suggest to assert that we are dealing with c++17, in case we are using std::fopen.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-01-29 03:01:17 UTC  
> Url: https://github.com/boostorg/beast/pull/999#issuecomment-361127552  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/999?src=pr&el=h1) Report  
> Merging [#999](https://codecov.io/gh/boostorg/beast/pull/999?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/908f84cb3c023ed7c5bd19e2e5283d3218a6d820?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/999/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/999?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #999      +/-   ##  
===========================================  
+ Coverage    95.43%   95.44%   +<.01%       
===========================================  
  Files          106      106                
  Lines        10614    10615       +1       
===========================================  
+ Hits         10130    10131       +1       
  Misses         484      484  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/999?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/file\_stdio.ipp](https://codecov.io/gh/boostorg/beast/pull/999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9zdGRpby5pcHA=) | `75.7% <100%> (+0.22%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/999?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/999?src=pr&el=footer). Last update [908f84c...e7ae0d2](https://codecov.io/gh/boostorg/beast/pull/999?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-02-15 03:27:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/999#pullrequestreview-96728869  

📁 include/boost/beast/core/impl/file_stdio.ipp

```diff
 108 |+     #if (__cplusplus <= 201402L)
 109 |+         // supported by std::fopen since c++17
 110 |+         BOOST_ASSERT(mode != file_mode::write_new);
```

> Username: vinniefalco  
> Created_at: 2018-02-15 03:27:33 UTC  
> Url: https://github.com/boostorg/beast/pull/999#discussion_r168373805  

How about if we make the `file_mode::write_new` constant available only in C++17 and later? And the documentation can reflect this?


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2018-02-15 03:28:29 UTC  
> Updated_at: 2018-02-15 03:28:42 UTC  
> Url: https://github.com/boostorg/beast/pull/999#issuecomment-365816151  

I think it would be better to make `write_new` available only in C++17 this way they get an error at compile time instead of an assertion at run-time. And no one can accidentally make a program using Beast that only works in C++17 yet still compiles in C++11. What do you think?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-02-16 19:16:55 UTC  
> Url: https://github.com/boostorg/beast/pull/999#issuecomment-366332165  

Hmm this problem is a bit more nuanced than I originally thought. Only `file_stdio` doesn't support `write_new` on C++11, the other file implementations do support it (`file_win32` and `file_posix`). So the constant cannot be made conditionally available at compile time depending on language version.  
  
Furthermore, `__cplusplus` is not always a reliable method of checking for features. We should define a macro, say `BOOST_BEAST_HAS_C11_FILE_MODES` and if that is not set, then throw an exception on `write_new` and `append_new`. Then we can work out the logic for setting that macro separately. The macro should be documented.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-12-21 21:38:53 UTC  
> Url: https://github.com/boostorg/beast/pull/999#issuecomment-449504827  

This took forever, but I have added code to deal with the missing modes in some cases. And I have removed the superfluous file mode (`append_new`.. lol). However I think this case is still a problem. Availability of `"wbx"` mode is a C level feature not C++, I think. I'm not sure this is the right way to test it. I am investigating...

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2018-12-21 21:47:34 UTC  
> Updated_at: 2018-12-21 21:47:44 UTC  
> Url: https://github.com/boostorg/beast/pull/999#issuecomment-449506406  

I believe this can be fixed by emulating the mode. In fact I have already merged code to do so:  
https://github.com/boostorg/beast/blob/f00237cb35bcb0d3d544db4b80de38f1aba78ab0/include/boost/beast/core/impl/file_stdio.hpp  
  
The problem is that the condition `__cplusplus <= 201402L` is not necessarily indicative of lack of support for the feature. For example, my `_MSC_VER` is 1910, my `__cplusplus` is less than `201402L`, but "wbx" works.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2019-02-07 16:39:00 UTC  
> Url: https://github.com/boostorg/beast/pull/999#issuecomment-461502836  

I believe this is resolved in the **master** and **develop** branches, could you please try it?

---
