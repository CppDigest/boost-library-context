# #19 Enable variadic macros for PGI compilers [Closed]

> Username: dkolsen-pgi  
> Created at: 2018-09-10 21:15:59 UTC  
> Updated at: 2018-09-24 14:14:39 UTC  
> Closed at: 2018-09-24 14:14:39 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/19  

Remove PGI compilers with an EDG front end from the list of untested  
compilers for which BOOST_PP_VARIADICS is always false.  The PGI C++  
compiler implements variadic macros correctly in C++11 mode or later.  
(BOOST_PP_VARIADICS is still false for the PGI C compiler, which is not  
EDG based and which has an incomplete implementation of variadic  
macros.)

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-09-23 13:10:28 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/19#issuecomment-423815451  

I can manually add this fix since another change to config.hpp which I made created a conflict for your change. However we don't have a regression test for the pgi compiler nor does it seem supported by either travis ci or appveyor. So I do not have any way of testing this except locally and I only have the latest free version of pgi to test.

---

## Comment 2

> Username: dkolsen-pgi  
> Created_at: 2018-09-23 14:27:08 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/19#issuecomment-423820499  

If you manually add this fix (with whatever local testing you deem appropriate), I will do more thorough testing with the PGI compiler as soon as the change is in the develop branch.  I will submit a new pull request promptly if there is any problem.  The latest free version of PGI is good enough for your local testing; variadic macro support has been in PGI for at least several years.  The change is simple enough that I think the chances of things getting messed up by your manually adding this fix is small.

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-09-24 01:39:18 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/19#issuecomment-423863569  

I manually made the fix and pushed it to 'develop'. Please test. Thank you !

---
