# #23 Warning fixes for conversion warnings [Merged]

> Username: joachim-faulhaber  
> Created at: 2015-09-21 15:48:57 UTC  
> Updated at: 2015-10-17 05:32:03 UTC  
> Merged at: 2015-10-17 04:27:18 UTC  
> Closed at: 2015-10-17 04:27:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/23  



---

## Comment 1

> Username: eldiener  
> Created_at: 2015-09-23 00:49:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/23#issuecomment-142463959  

There are errors in your PR. Please fix. You have both less and more parentheses than you should in certain cases.

---

## Comment 2

> Username: zerkms  
> Created_at: 2015-10-07 22:50:20 UTC  
> Url: https://github.com/boostorg/date_time/pull/23#issuecomment-146354759  

Maybe I'm missing something, but what is the point to declare a variable as `unsigned long week` and then cast it to integer in `return static_cast<int>(week)`?  
  
Does not it always fit an `int` size at the very first place?

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-10-17 04:26:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/23#issuecomment-148885005  

You have a point that 'week' could have been an 'int'. If you feel strongly about it can you create a separate PR for it ?

---

## Comment 4

> Username: zerkms  
> Created_at: 2015-10-17 05:32:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/23#issuecomment-148887098  

@eldiener I technically could, I'm dealing with C++ for few last weeks so I don't feel confident to propose such significant (?) changes.

---
