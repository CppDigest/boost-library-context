# #213 - has_cxx11 : False When building from VS2017 console [Closed]

> Username: illera88  
> Created at: 2018-06-29 23:23:36 UTC  
> Updated at: 2018-06-30 18:23:19 UTC  
> Closed at: 2018-06-30 18:23:19 UTC  
> Url: https://github.com/boostorg/python/issues/213  

Why do I get that:  
```  
C:\Users\user\Documents\code\python>C:\Python37-x64\python.exe ..\faber\scripts\faber  
configuration check results:  
  has_cxx11 : False  
  has_numpy : True  
```  
thank you

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-06-30 14:16:27 UTC  
> Url: https://github.com/boostorg/python/issues/213#issuecomment-401543949  

because the compiler reports that it isn't (fully) C++11 compliant ? (The check tests the value of `__cplusplus`, which MSVC deliberately doesn't bump.)

---

## Comment 2

> Username: illera88  
> Created at: 2018-06-30 17:48:09 UTC  
> Url: https://github.com/boostorg/python/issues/213#issuecomment-401556134  

What are the implications of that? Because I see bunch of stuff that get''s not compiled without c++11.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-06-30 18:23:19 UTC  
> Url: https://github.com/boostorg/python/issues/213#issuecomment-401557958  

If you have any specific issue (with the Boost.Python libs not supporting particular features), please report them. Otherwise I'm not sure what to say. This issue as stated is too vague for me to act upon. Boost.Python doesn't require C++11, so I'm not entirely sure what you are asking.
