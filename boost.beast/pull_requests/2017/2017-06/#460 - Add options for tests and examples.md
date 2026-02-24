# #460 Add options for tests and examples [Closed]

> Username: xsacha  
> Created at: 2017-06-10 02:43:55 UTC  
> Updated at: 2017-06-19 22:43:31 UTC  
> Closed at: 2017-06-19 22:43:31 UTC  
> Url: https://github.com/boostorg/beast/pull/460  

#442

---

## Comment 1

> Username: xsacha  
> Created_at: 2017-06-10 02:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307537574  

Added #443

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-06-10 03:32:49 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307539045  

I don't understand CMake well enough to evaluate these changes but an "install" is sort of out of scope, as this is being proposed as a Boost library and therefore, does not require an "install" target since it will adhere to the Boost directory structure.

---

## Comment 3

> Username: xsacha  
> Created_at: 2017-06-10 03:35:46 UTC  
> Updated_at: 2017-06-10 03:52:00 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307539184  

If it were to be included in Boost, then it would not need a CMakeLists.txt, correct? Boost unfortunately doesn't use CMake. So I am not sure how it is related.  
https://github.com/boostorg/build  
  
It installed the headers and the target/config file. So when another project does find_package(Beast), they can link Beast::Beast and get the headers and boost system libs+includes linked.

---

## Comment 4

> Username: xsacha  
> Created_at: 2017-06-10 03:45:01 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307539548  

I have switched back to a single commit.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-06-10 04:09:03 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307540592  

>If it were to be included in Boost, then it would not need a CMakeLists.txt  
  
Its not required, but I need CMakeLists.txt so I can generate Visual Studio project files (.vcxproj). That's my primary development environment.

---

## Comment 6

> Username: xsacha  
> Created_at: 2017-06-10 04:10:37 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307540682  

OK. So what's the problem? It will still make these .vcxproj :)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-06-10 04:16:58 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307541029  

Let me make sure I understand this (I am no CMake expert) - this change only adds the option of not building the examples or tests?

---

## Comment 8

> Username: xsacha  
> Created_at: 2017-06-10 04:18:02 UTC  
> Updated_at: 2017-06-10 04:19:59 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307541077  

Yes and they are enabled by default.  
To build without examples, you could do:  
`cmake -DBeast_BUILD_EXAMPLES=OFF ..`  
or uncheck the option in cmake-gui

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2017-06-10 16:32:15 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307575766  

While these changes look like they do what is intended, this is why I think we should not merge them:  
  
* They have little meaning when Beast becomes part of Boost  
  
* The build options and install target are not part of my workflow, and not part of the continuous integration scripts, so they will not receive testing. I won't know if I break them. Which means, they will break.

---

## Comment 10

> Username: xsacha  
> Created_at: 2017-06-11 01:30:01 UTC  
> Updated_at: 2017-06-11 01:30:46 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307600077  

I think this whole file will have little meaning when Beast becomes part of Boost unless it is still used for Travis CI (but I think Boost uses some other CI).  
What sort of timeline are you looking at for integration? If it is 6 months+, I still see value in this.  
  
These options should not be hard to maintain but I will fix them up if needed. It should be as simple as adding new tests inside the if (Beast_BUILD_TESTS) and new examples inside the if (Beast_BUILD_EXAMPLES).

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2017-06-11 02:46:46 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307602513  

The formal review is July 1st, just 19 days!

---

## Comment 12

> Username: xsacha  
> Created_at: 2017-06-11 03:09:59 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307603238  

Oh I see! But the project isn't even API complete yet?

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2017-06-11 03:11:21 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307603280  

>But the project isn't even API complete yet?  
  
It is **very** close to being complete.

---

## Comment 14

> Username: codecov[bot]  
> Created_at: 2017-06-11 04:31:12 UTC  
> Updated_at: 2017-06-11 04:31:35 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-307605781  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/460?src=pr&el=h1) Report  
> Merging [#460](https://codecov.io/gh/vinniefalco/Beast/pull/460?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/18a8ef5a3b015e1817c5528d30f4f07266321efd?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/460/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/460?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           master     #460   +/-   ##  
=======================================  
  Coverage   93.19%   93.19%             
=======================================  
  Files          92       92             
  Lines        6741     6741             
=======================================  
  Hits         6282     6282             
  Misses        459      459  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/460?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/460?src=pr&el=footer). Last update [18a8ef5...bce23e2](https://codecov.io/gh/vinniefalco/Beast/pull/460?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 15

> Username: xsacha  
> Created_at: 2017-06-19 22:43:31 UTC  
> Url: https://github.com/boostorg/beast/pull/460#issuecomment-309594056  

@ksergey has a newer version of this rebased on v61 so this can be closed.

---
