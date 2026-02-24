# #461 - "pickle1" and "pickle4" unit tests fail with Python 3.11 [Closed]

> Username: sunyab  
> Created at: 2024-12-09 20:32:02 UTC  
> Updated at: 2025-10-25 18:14:08 UTC  
> Closed at: 2025-10-25 18:14:08 UTC  
> Url: https://github.com/boostorg/python/issues/461  

Both "pickle1" and "pickle4" fail when boost::python is built against Python 3.11 or later. I've pasted in the test output below.  
  
I believe these failures are due to the addition of `object.__getstate__` documented in this section: https://docs.python.org/3/whatsnew/3.11.html#other-language-changes  
  
```  
====== BEGIN OUTPUT ======  
running...  
**********************************************************************  
File "/root/boost_1_86_0/libs/python/test/pickle1.py", line 12, in __main__  
Failed example:  
    pickle1_ext.world('Hello').__reduce__()  
Expected:  
    (<class 'pickle1_ext.world'>, ('Hello',))  
Got:  
    (<class 'pickle1_ext.world'>, ('Hello',), None)  
**********************************************************************  
1 items had failures:  
   1 of  13 in __main__  
***Test Failed*** 1 failures.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
```  
====== BEGIN OUTPUT ======  
running...  
**********************************************************************  
File "/root/boost_1_86_0/libs/python/test/pickle4.py", line 15, in __main__  
Failed example:  
    pickle4_ext.world('Hello').__reduce__()  
Expected:  
    (<class 'pickle4_ext.world'>, ('Hello',))  
Got:  
    (<class 'pickle4_ext.world'>, ('Hello',), None)  
**********************************************************************  
1 items had failures:  
   1 of  13 in __main__  
***Test Failed*** 1 failures.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
This was originally reported here: https://github.com/PixarAnimationStudios/OpenUSD/issues/3384
