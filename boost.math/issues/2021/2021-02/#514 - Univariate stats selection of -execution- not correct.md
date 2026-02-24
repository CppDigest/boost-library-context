# #514 - Univariate stats selection of <execution> not correct. [Closed]

> Username: jzmaddock  
> Created at: 2021-02-02 18:24:49 UTC  
> Updated at: 2021-02-07 08:22:00 UTC  
> Closed at: 2021-02-07 08:22:00 UTC  
> Url: https://github.com/boostorg/math/issues/514  

In this PR to add drone testing (https://github.com/boostorg/math/pull/508), there are several failures due to clang not have <execution>: see for example https://drone.cpp.al/boostorg/math/10/74/2.  
  
I think the logic is wrong in that even very recent clang (this was clang-10) may be running on top of a much older system libstdc++.  In addition I assume that libc++ will eventually get <execution> where as the current logic will *never* enable it's use with XCode for example.  
  
Is there any reason not to use BOOST_NO_CXX17_HDR_EXECUTION from Boost.Config here?  It already has the logic we need.  
  
@mborland  : If you're happy with that, I can just add the logic to this PR.

---

## Comment 1

> Username: mborland  
> Created at: 2021-02-02 18:36:35 UTC  
> Updated at: 2021-02-02 18:38:28 UTC  
> Url: https://github.com/boostorg/math/issues/514#issuecomment-771875102  

You could try it, but I believe you will get a rash of new failures. For at least GCC-9 you have to link TBB in order to fully provide `<execution>`. I could not get the TBB builds to work and they all failed t-san. If you use libstdc++ provided by gcc-10 (which does not use TBB) you have none of these issues.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-02 18:45:36 UTC  
> Url: https://github.com/boostorg/math/issues/514#issuecomment-771882183  

OK let me try and see, we can always disable gcc-9 as a special case.

---

## Comment 3

> Username: mborland  
> Created at: 2021-02-02 18:58:10 UTC  
> Url: https://github.com/boostorg/math/issues/514#issuecomment-771891852  

That same macro also appears in `bivariate_statistics` and the test files for both.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-02-03 19:03:18 UTC  
> Url: https://github.com/boostorg/math/issues/514#issuecomment-772744981  

Use of BOOST_NO_CXX17_EXECUTION should all be working in the referenced PR now: I had to make some corrections in Boost.Config for latest XCode-12 which has a non-conforming <execution> apparently.  GCC-9/10 were already handled with gcc-9 checking to see if TBB is installed or not and setting the macro appropriately.

---

## Comment 5

> Username: mborland  
> Created at: 2021-02-03 19:13:02 UTC  
> Url: https://github.com/boostorg/math/issues/514#issuecomment-772750963  

I do remember XCode giving problems which was why the glibcxx part got added in for clang. Thanks for correcting my logic.
