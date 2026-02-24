# #69 - warning on duplicate macro definitions [Closed]

> Username: vinniefalco  
> Created at: 2018-03-23 17:54:20 UTC  
> Updated at: 2018-03-23 18:25:14 UTC  
> Closed at: 2018-03-23 18:25:14 UTC  
> Url: https://github.com/boostorg/winapi/issues/69  

I believe these are caused by Boost.WinAPI  
  
http://www.boost.org/development/tests/develop/output/teeks99-09-f-win2016-64on64-beast-msvc-14-0-warnings.html#accept

---

## Comment 1

> Username: Lastique  
> Created at: 2018-03-23 18:25:14 UTC  
> Url: https://github.com/boostorg/winapi/issues/69#issuecomment-375758711  

Yes, this will be fixed when all dependent libraries are ported to Boost.WinAPI new macros in master, which will happen after 1.67 is out.
