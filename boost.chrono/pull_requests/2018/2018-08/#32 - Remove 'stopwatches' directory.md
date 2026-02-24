# #32 Remove 'stopwatches' directory [Closed]

> Username: mclow  
> Created at: 2018-08-08 17:05:47 UTC  
> Updated at: 2018-09-07 03:53:44 UTC  
> Closed at: 2018-09-07 03:19:29 UTC  
> Url: https://github.com/boostorg/chrono/pull/32  

Vicente said (May 16th) that "I'm really sorry for that. Boost.Chrono/Stopwatches was never released even if it was included in the Boost repository" and "I'll remove it definitively from the boost repository."

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-08-13 16:47:22 UTC  
> Url: https://github.com/boostorg/chrono/pull/32#issuecomment-412585360  

Note that stopwatches are referenced in https://github.com/boostorg/chrono/blob/develop/test/cycle_count.hpp, https://github.com/boostorg/chrono/blob/develop/example/chrono_accuracy_test.cpp#L14 and https://github.com/boostorg/thread/blob/develop/example/perf_condition_variable.cpp#L14. Also, there are stopwatches-related bits in https://github.com/boostorg/chrono/blob/develop/meta/libraries.json and https://github.com/boostorg/chrono/blob/develop/doc/chrono.qbk. These places need to be cleaned up as well.

---

## Comment 2

> Username: viboes  
> Created_at: 2018-08-14 05:52:49 UTC  
> Url: https://github.com/boostorg/chrono/pull/32#issuecomment-412762168  

Yes, this need a more careful cleanup.

---

## Comment 3

> Username: viboes  
> Created_at: 2018-09-07 03:17:10 UTC  
> Url: https://github.com/boostorg/chrono/pull/32#issuecomment-419309544  

I'm taking care of removing all the adherence to stopwatches folder.

---

## Comment 4

> Username: mclow  
> Created_at: 2018-09-07 03:19:29 UTC  
> Url: https://github.com/boostorg/chrono/pull/32#issuecomment-419309938  

> I'm taking care of removing all the adherence to stopwatches folder.  
  
In that case, I will close this P/R.

---

## Comment 5

> Username: viboes  
> Created_at: 2018-09-07 03:53:44 UTC  
> Url: https://github.com/boostorg/chrono/pull/32#issuecomment-419314663  

Hi,  
  
I've just merged this  
  
https://github.com/boostorg/chrono/commit/ce6286ae6eca8531f79c58745a7e1c83ebdc120f  
  
Marshall,I had some troubles with my quickbook installation and I was unable to check the doc changes. Please, could you check that doc generation has not been broken?  
  
Thanks for recall me to do this due cleanup.

---
