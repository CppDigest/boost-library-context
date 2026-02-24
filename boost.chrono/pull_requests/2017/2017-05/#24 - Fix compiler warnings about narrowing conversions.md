# #24 Fix compiler warnings about narrowing conversions [Merged]

> Username: DanielaE  
> Created at: 2017-05-01 18:02:44 UTC  
> Updated at: 2018-01-02 10:22:55 UTC  
> Merged at: 2017-05-03 18:08:18 UTC  
> Closed at: 2017-05-03 18:08:18 UTC  
> Url: https://github.com/boostorg/chrono/pull/24  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: viboes  
> Created_at: 2017-05-01 18:44:36 UTC  
> Url: https://github.com/boostorg/chrono/pull/24#issuecomment-298399428  

I understand your concern with the new Wnarrowing warnings, but doesn't this hide a possible bug on the user side?  
  
I believe this is something that must be managed by a safe_int class that will do whatever is needed.   
  
What do you think?

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-05-02 10:03:01 UTC  
> Url: https://github.com/boostorg/chrono/pull/24#issuecomment-298587810  

Hi Vicente!  
  
when I look at the outcome of the testsuite I can see that there are cases where a user can do better when calling the chrono API. But there are also cases where users left on the mercy of the chrono's implementation.  
  
Running the suite (msvc 14.1) with today's vanilla `develop`, I get these warnings after grepping, sorting and uniquing the build logs:  
```  
boost/chrono/duration.hpp(459,1): warning C4244: 'initializing': conversion from 'const double' to 'float', possible loss of data  
boost/chrono/duration.hpp(459,1): warning C4244: 'initializing': conversion from 'const int' to 'float', possible loss of data  
boost/chrono/duration.hpp(459,1): warning C4244: 'initializing': conversion from 'const intermediate_type' to 'float', possible loss of data  
boost/chrono/duration.hpp(459,1): warning C4244: 'initializing': conversion from 'const intermediate_type' to 'int', possible loss of data  
boost/chrono/io/duration_get.hpp(312,1): warning C4244: 'initializing': conversion from 'common_type_t' to 'float', possible loss of data  
boost/chrono/io/duration_get.hpp(312,1): warning C4244: 'initializing': conversion from 'common_type_t' to 'int_least32_t', possible loss of data  
boost/chrono/io_v1/chrono_io.hpp(552,1): warning C4244: 'initializing': conversion from 'common_type_t' to 'float', possible loss of data  
boost/chrono/io_v1/chrono_io.hpp(552,1): warning C4244: 'initializing': conversion from 'common_type_t' to 'int_least32_t', possible loss of data  
```  
After assessing each individual warning for possible improvements on the caller side, using matching types for all input arguments when calling API functions from the affected test routines, I get these warnings when running the testsuite:  
```  
boost/chrono/duration.hpp(459,1): warning C4244: 'initializing': conversion from 'const intermediate_type' to 'int', possible loss of data  
boost/chrono/io/duration_get.hpp(312,1): warning C4244: 'initializing': conversion from 'common_type_t' to 'int_least32_t', possible loss of data  
boost/chrono/io_v1/chrono_io.hpp(552,1): warning C4244: 'initializing': conversion from 'common_type_t' to 'int_least32_t', possible loss of data  
```  
There are less warnings in total but the remaining ones are totally outside user control, and as it turns out the failure locations remain the same.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-05-02 20:48:12 UTC  
> Url: https://github.com/boostorg/chrono/pull/24#issuecomment-298756461  

I would like to fix any issue the library can have, however I'm sure this is the way to do it.  
  
Please, could you add an issue where you add examples probing what you say, so that I can see if the problem is on the user side or on the chrono side?  
  
You can also add the examples to your patch if you prefer.

---

## Comment 4

> Username: DanielaE  
> Created_at: 2017-05-03 07:30:01 UTC  
> Url: https://github.com/boostorg/chrono/pull/24#issuecomment-298840811  

Ok, please see [issue #25](https://github.com/boostorg/chrono/issues/25).

---

## Comment 5

> Username: DanielaE  
> Created_at: 2017-05-03 14:19:19 UTC  
> Url: https://github.com/boostorg/chrono/pull/24#issuecomment-298924824  

So what about this?

---

## Comment 6

> Username: viboes  
> Created_at: 2017-05-03 18:08:01 UTC  
> Updated_at: 2017-05-03 18:08:58 UTC  
> Url: https://github.com/boostorg/chrono/pull/24#issuecomment-298990213  

Much better. Thanks a lot.

---
