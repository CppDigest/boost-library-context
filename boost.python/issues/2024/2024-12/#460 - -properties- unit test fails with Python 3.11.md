# #460 - "properties" unit test fails with Python 3.11 [Closed]

> Username: sunyab  
> Created at: 2024-12-09 20:21:04 UTC  
> Updated at: 2025-10-25 19:22:22 UTC  
> Closed at: 2025-10-25 19:22:22 UTC  
> Url: https://github.com/boostorg/python/issues/460  

The "properties" unit test fails when boost::python is built against Python 3.11 or later. I've pasted in the test output below (note: the output mentions 3.12, but I've tested this against 3.11 as well).  
  
I believe this failure is due to Python now including more detail in tracebacks -- see https://docs.python.org/3/whatsnew/3.11.html#whatsnew311-pep657  
  
```  
====== BEGIN OUTPUT ======  
running...  
**********************************************************************  
File "/root/boost_1_86_0/libs/python/test/properties.py", line 56, in __main__  
Failed example:  
    x1.value_r = 2  
Expected:  
    Traceback (most recent call last):  
      File "properties.py", line 49, in ?  
        x1.value_r = 2  
    AttributeError: can't set attribute  
Got:  
    Traceback (most recent call last):  
      File "/usr/lib/python3.12/doctest.py", line 1361, in __run  
        exec(compile(example.source, filename, "single",  
      File "<doctest __main__[16]>", line 1, in <module>  
        x1.value_r = 2  
        ^^^^^^^^^^  
    AttributeError: property of 'X' object has no setter  
**********************************************************************  
1 items had failures:  
   1 of  28 in __main__  
***Test Failed*** 1 failures.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
This was originally reported here: https://github.com/PixarAnimationStudios/OpenUSD/issues/3384
