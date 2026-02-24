# #279 Added temporary vector to adjacent_difference(). [Merged]

> Username: pentschev  
> Created at: 2014-10-16 14:45:07 UTC  
> Updated at: 2014-10-23 03:14:03 UTC  
> Merged at: 2014-10-23 03:13:06 UTC  
> Closed at: 2014-10-23 03:13:06 UTC  
> Url: https://github.com/boostorg/compute/pull/279  

Fixes issue #278.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-10-16 17:10:59 UTC  
> Url: https://github.com/boostorg/compute/pull/279#issuecomment-59396199  

[![Coverage Status](https://coveralls.io/builds/1344913/badge)](https://coveralls.io/builds/1344913)  
  
Coverage decreased (-0.03%) when pulling **c117648c4cf4302a55feb6e265a329791a9ce601 on pentschev:adjacent_difference_temp_vector** into **eb881491ea3c13d48e6e6bfb221082a72b83c59e on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-10-23 03:06:57 UTC  
> Url: https://github.com/boostorg/compute/pull/279#discussion_r19257497  

Just FYI, you could also use the iterator-range constructor for [`vector<T>`](http://kylelutz.github.io/compute/boost/compute/vector.html) to do this in one line:  
  
```  
vector<value_type> temp(first, last, queue);  
```  
  
This will both construct the vector with the proper size and copy the data from the range `[first, last)`.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-10-23 03:14:03 UTC  
> Url: https://github.com/boostorg/compute/pull/279#issuecomment-60187551  

Sorry for the delay, I've been away on vacation for the past couple weeks.  
  
Thanks for fixing this! Merged!

---
