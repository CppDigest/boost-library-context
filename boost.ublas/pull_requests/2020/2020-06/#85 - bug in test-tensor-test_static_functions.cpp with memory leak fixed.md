# #85 bug in test/tensor/test_static_functions.cpp with memory leak fixed. [Merged]

> Username: bassoy  
> Created at: 2020-06-02 16:02:11 UTC  
> Updated at: 2020-07-18 12:42:15 UTC  
> Merged at: 2020-06-23 12:59:52 UTC  
> Closed at: 2020-06-23 12:59:52 UTC  
> Url: https://github.com/boostorg/ublas/pull/85  

Using `valgrind` there were invalid reads in `test_static_functions.cpp` coming from line 158.  
There are small bug fixes involved.  
Many changes are due to formatting.

---

## Review 1 [Approved]

> Username: amitsingh19975  
> Created_at: 2020-06-02 16:10:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/85#pullrequestreview-422838135  

I think because of this bug I was having a hard time with CI. Thanks for the fix and it looks much better than my code and cleaner.

---

## Comment 2

> Username: bassoy  
> Created_at: 2020-06-02 16:28:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/85#issuecomment-637665266  

> I think because of this bug I was having a hard time with CI. Thanks for the fix and it looks much better than my code and cleaner.  
  
you did a very good job. all our code will improve by time. please feel free to critize my part.

---

## Comment 3

> Username: bassoy  
> Created_at: 2020-06-23 12:59:27 UTC  
> Url: https://github.com/boostorg/ublas/pull/85#issuecomment-648129105  

I will now squash and merge. AppVeyor passed for c++17 and for the tensor tests.

---
