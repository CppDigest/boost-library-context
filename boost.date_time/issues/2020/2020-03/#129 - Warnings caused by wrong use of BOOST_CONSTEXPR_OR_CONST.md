# #129 - Warnings caused by wrong use of BOOST_CONSTEXPR_OR_CONST [Closed]

> Username: Lastique  
> Created at: 2020-03-09 23:38:06 UTC  
> Updated at: 2020-03-11 18:58:29 UTC  
> Closed at: 2020-03-11 18:58:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/129  

CC @JeffGarland   
  
Since commit https://github.com/boostorg/date_time/commit/4e1b7cde45edf8fdda73ec5c60053c9257138292 gcc 9 shows warnings like the following:  
  
```  
./boost/date_time/time_duration.hpp:85:12: warning: 'const' type qualifier on return type has no effect [-Wignored-qualifiers]  
  
    static BOOST_CONSTEXPR_OR_CONST time_resolutions resolution()  
  
           ^~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
The problem is that the commit marked many functions with `BOOST_CONSTEXPR_OR_CONST` instead of `BOOST_CONSTEXPR`, which results in the functions returning const values in C++03.  
  
`BOOST_CONSTEXPR_OR_CONST` is intended to declare constant variables, it is not supposed to be used with functions.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-03-10 14:13:08 UTC  
> Url: https://github.com/boostorg/date_time/issues/129#issuecomment-597108016  

ok thanks - was working out how to use the macros correctly by trial and error as your point wasn't clear from the docs.  
  
I am disturbed, however, that none of the CI or test my gcc9 test builds showed me this regression in a way that made me pay attention.  Before I ultimately committed the change I had compiled 98, 11, 14, 17, and 2a variants on my machine -- and of course the CI ran successfully.  So where is it that you're seeing the warnings and why am I not?  Maybe @jeking3 can help us update the CI to fail on warnings under at least some configurations?

---

## Comment 2

> Username: Lastique  
> Created at: 2020-03-10 15:44:36 UTC  
> Url: https://github.com/boostorg/date_time/issues/129#issuecomment-597158240  

> So where is it that you're seeing the warnings and why am I not?  
  
You need to actually call (or otherwise ODR-use) the problematic functions, and you need to look into the successful build log (normally, the build will not fail because of warnings). I saw them in my Boost.Atomic builds, but the warnings are actually triggered by Boost.Thread. See [here](https://travis-ci.org/boostorg/atomic/jobs/660251925#L931) for example.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-03-10 15:59:00 UTC  
> Updated at: 2020-03-10 16:00:44 UTC  
> Url: https://github.com/boostorg/date_time/issues/129#issuecomment-597166226  

Oh, and I mistook the compiler - it's clang 9, not gcc 9. Sorry.

---

## Comment 4

> Username: JeffGarland  
> Created at: 2020-03-10 17:42:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/129#issuecomment-597221266  

Thanks for the pointers -- I'll check if somehow we don't have a test for those.    
  
> need to look into the successful build log  
  
Yeah, that's not a very scalable solution -- the build logs are enormous and tedious to scroll thru.  There are more major changes in work so I need to be able to do this reliably. We either need to look at changing warnings into errors on the CI or apparently I'll need to install clang to run this locally.

---

## Comment 5

> Username: JeffGarland  
> Created at: 2020-03-10 23:26:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/129#issuecomment-597368883  

Fixed - looked at the CI for clang and didn't see errors after the pull.  Will keep open so we can see that it resolves.  Did check that there are date-time test functions for many of these functions that are complaining -- although apparently not on gcc.

---

## Comment 6

> Username: Lastique  
> Created at: 2020-03-11 18:58:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/129#issuecomment-597810771  

Thanks. Recent [build](https://travis-ci.org/github/boostorg/atomic/jobs/660788518) of Boost.Atomic does not show these warnings.
