# #42 Fixed integer overflow when subtracting many years to a gregorian date. [Merged]

> Username: Lastique  
> Created at: 2017-08-08 14:45:13 UTC  
> Updated at: 2017-08-08 14:50:44 UTC  
> Merged at: 2017-08-08 14:47:48 UTC  
> Closed at: 2017-08-08 14:47:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/42  

Also added a testcase to detect the overflow.  
  
This is in continuation for https://github.com/boostorg/date_time/pull/41, which fixed the same problem for addition. Sorry I didn't notice the problem with subtraction earlier.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-08-08 14:49:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/42#issuecomment-320979781  

Thanks. Again, please watch the testbots to make sure no problems show up in the tests, and ping me after the release to merge this into master.

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-08-08 14:50:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/42#issuecomment-320980075  

Ok.

---
