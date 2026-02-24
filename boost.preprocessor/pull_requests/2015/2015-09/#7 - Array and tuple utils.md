# #7 Array and tuple utils [Closed]

> Username: nicuveo  
> Created at: 2015-09-11 13:59:55 UTC  
> Updated at: 2019-08-29 22:32:34 UTC  
> Closed at: 2019-08-29 22:32:34 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7  

This patch adds the following functions for both tuples and arrays:  
- CAT, which concatenates;  
- LOWER_BOUND, which searches for the lower bound in a sorted container of numerical values;  
- SORT, which sorts a container of numerical values;  
- SORT_U, which sorts and removes duplicate values.

---

## Comment 1

> Username: nicuveo  
> Created_at: 2015-09-11 20:21:43 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-139653373  

The tests in this pull request do not pass yet. I'll update them ASAP.

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-09-11 22:38:12 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-139678373  

Take your time. There is no rush.

---

## Comment 3

> Username: nicuveo  
> Created_at: 2015-09-12 17:38:05 UTC  
> Updated_at: 2015-09-12 17:38:39 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-139799423  

Updated! I made one commit per new macro, and made sure each commit passes all tests. I also added ARRAY_EQUAL and TUPLE_EQUAL.

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-09-14 16:47:38 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-140141460  

My testing shows that when using Visual C++ I receive:  
  
"fatal error C1060: compiler is out of heap space" for:  
  
```  
BEGIN BOOST_PP_ARRAY_EQUAL(BOOST_PP_ARRAY_SORT(TEST_AS_4),   (12, (0, 1, 1, 2, 3, 3, 4, 4, 5, 5, 6, 12))) == 1 END  
```  
  
and  
  
```  
BEGIN BOOST_PP_TUPLE_EQUAL(BOOST_PP_TUPLE_SORT(TEST_TS_4),    (0, 1, 1, 2, 3, 3, 4, 4, 5, 5, 6, 12)) == 1 END  
```

---

## Comment 5

> Username: nicuveo  
> Created_at: 2015-09-14 16:52:45 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-140142606  

Okay, I had only tested with g++ and clang++ under Linux. I'll try to find some way to try with a Windows, to see what's wrong. Thanks for the feedback!

---

## Comment 6

> Username: eldiener  
> Created_at: 2015-09-14 23:58:54 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-140236152  

After looking at your discard.hpp macros I decided to add to Boost PP 'develop' a macro called BOOST_PP_IDENTITY_N(item,n) which you can use in place of the BOOST_PP_DISCARD functions. First try to get your macros to work with VC++, and then if you do switch to using BOOST_PP_IDENTITY_N and get rid of your discard.hpp header.   
  
The equivalent to any of your BOOST_PP_DISCARD_n functions is:  
  
```  
BOOST_PP_IDENTITY_N(,n)  
```  
  
The equivalent to any of your BOOST_PP_DISCARD_MIN_n functions is:  
  
```  
BOOST_PP_IDENTITY_N(0,n)  
```  
  
The equivalent to any of your BOOST_PP_DISCARD_MAX_n functions is:  
  
```  
BOOST_PP_IDENTITY_N(BOOST_PP_LIMIT_MAG,n)  
```  
  
If you have trouble finding a workaround for VC++ I can try to do so myself. The VC++ non-standard C++ preprocessor is the major cause of programming problems when creating macros for Boost PP and Boost VMD and I have quite a bit of experience coming up with workarounds for it. So don't be dismayed if you can't figure out how to get around its cranky behavior.

---

## Comment 7

> Username: nicuveo  
> Created_at: 2015-09-15 07:46:46 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-140310368  

Okay about IDENTITY! Regarding VC++, I thank you for the offer: it'll be a few days until I can test under Windows. However, I fear it is simply that sorts are expensive and that I might have to find another way to implement them...

---

## Comment 8

> Username: nicuveo  
> Created_at: 2015-12-12 20:41:07 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-164189248  

Hi, sorry for leaving this hanging for so long. I now have access to a Windows machine, and will resume working on it.

---

## Comment 9

> Username: nicuveo  
> Created_at: 2015-12-12 21:57:38 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-164196621  

Okay, I can reproduce the problem. I don't know how exactly VC++'s preprocessor differs from clang++/g++; would you have any pointer on how to work around that kind of issue?

---

## Comment 10

> Username: eldiener  
> Created_at: 2015-12-16 16:56:56 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-165174040  

The problem with VC++ is that it does not expand macros according to the C++ standard, so you are always guessing what it is doing. Furthermore VC++ has never included any tool by which you can easily follow the steps the preprocessor takes when it expands a macro. Unfortunately it is nearly impossible to get VC++ to output a preprocessing error in order to see what it is doing. So your best means of trying to figure out what it is doing is to output the intermediate results of its macro expansion in some way. This could entail writing the output of some intermediate result to a file or to stdout. You can often use the preprocessor stringize operator to create a C-string ( ie. "some_data" ) to output an intermediate result.  
  
Other possibilities include:  
1) Use BOOST_PP_ASSERT_MSG to try to output a debugging message into the preprocessor output which you can look at. I have never gotten this to work with VC++ but you can play around with it and try it yourself.  
2) Use BOOST_VMD_ASSERT to output some sort of compile-time error which VC++ may or may not print out in full for you.  
3) Use the following macro to try to generate a compile-time error which VC++ may or may not print out for you:  
  
#if !defined(BOOST_PREPROCESSOR_SHOW_VC_EXPANSION_HPP)  
#define BOOST_PREPROCESSOR_SHOW_VC_EXPANSION_HPP  
#include <boost/preprocessor/config/config.hpp>  
#if BOOST_PP_CONFIG_FLAGS() & BOOST_PP_CONFIG_MSVC()  
#include <boost/preprocessor/cat.hpp>  
#include <boost/preprocessor/stringize.hpp>  
#define BOOST_PREPROCESSOR_SHOW_VC_EXPANSION(param) \  
    typedef char BOOST_PP_STRINGIZE(BOOST_PP_CAT(=,param))[-1]; \  
/*_/  
#else  
#define BOOST_PREPROCESSOR_SHOW_VC_EXPANSION(param)  
#endif  
#endif /_ BOOST_PREPROCESSOR_SHOW_VC_EXPANSION_HPP */  
  
VC++ is very hard to work with when doing preprocessor programming and Microsoft has endlessly made noises over the years about correcting their preprocessor to follow the C++ standard, but they have never done it. I don't think they ever will. It seems to be a point of pride with them that they can produce the most used C++ compiler in the world despite having a non-conformant C++ preprocessor. But who knows what may happen in the future.

---

## Comment 11

> Username: nicuveo  
> Created_at: 2015-12-20 16:00:33 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/7#issuecomment-166131423  

So, I've tried several things, including:  
- reducing as much as possible [the number of expansions](https://github.com/nicuveo/preprocessor/blob/f4bc4352a7b2f679628c75a2c629c089f816e4ed/include/boost/preprocessor/tuple/sort.hpp)  
- changing to a verbose [bubble sort](https://github.com/nicuveo/preprocessor/blob/f4bc4352a7b2f679628c75a2c629c089f816e4ed/include/boost/preprocessor/array/sort.hpp)  (removes the need for the LOWER_BOUND call)  
  
All my experiments have the same problem as the original version: VC++ just crashes when trying to sort arrays / tuples that contain ten or more elements...  
  
I'm very tempted to put those sorting functions behind some kind of a feature gate.

---
