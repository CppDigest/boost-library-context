# #405 - `import("__main__")` crashes on Windows in Debug mode [Closed]

> Username: stephengtuggy  
> Created at: 2022-12-11 00:42:48 UTC  
> Updated at: 2024-12-11 03:04:47 UTC  
> Closed at: 2023-01-16 01:53:35 UTC  
> Url: https://github.com/boostorg/python/issues/405  

As reported [here](https://stackoverflow.com/questions/24986361/boost-python-on-windows-crashes-at-import-main):  
  
Running `boost::python::import("__main__");` crashes on Windows, specifically when compiled in Debug configuration (not Release). This was on Windows 11 x64, with Python 3.10, I believe. Others have reported the same issue with other versions of Windows and of Python. This issue does not exist on Linux, as far as I can tell.  
  
A small program illustrating this bug can be found [here](https://github.com/stephengtuggy/PyPoc/blob/main/main.cpp).  
  
Thanks.

---

## Comment 1

> Username: Osyotr  
> Created at: 2022-12-24 13:10:55 UTC  
> Url: https://github.com/boostorg/python/issues/405#issuecomment-1364528392  

@stephengtuggy you might need to add  
```cmake  
target_compile_definitions(${target_name} PUBLIC "$<$<CONFIG:Debug>:BOOST_DEBUG_PYTHON>")  
target_compile_definitions(${target_name} PUBLIC "$<$<CONFIG:Debug>:Py_DEBUG>")  
```  
to your `CMakeLists.txt`.

---

## Comment 2

> Username: stephengtuggy  
> Created at: 2023-01-06 00:53:10 UTC  
> Url: https://github.com/boostorg/python/issues/405#issuecomment-1372991728  

> @stephengtuggy you might need to add  
>   
> ```cmake  
> target_compile_definitions(${target_name} PUBLIC "$<$<CONFIG:Debug>:BOOST_DEBUG_PYTHON>")  
> target_compile_definitions(${target_name} PUBLIC "$<$<CONFIG:Debug>:Py_DEBUG>")  
> ```  
>   
> to your `CMakeLists.txt`.  
  
@Osyotr Hmm. Interesting. When I make those changes, I am no longer able to reproduce the crash. At least not as reliably.  
  
Any idea what makes the difference?

---

## Comment 3

> Username: Osyotr  
> Created at: 2023-01-06 07:02:34 UTC  
> Url: https://github.com/boostorg/python/issues/405#issuecomment-1373219836  

ABI incompatibility.  
https://www.boost.org/doc/libs/1_81_0/libs/python/doc/html/building/python_debugging_builds.html

---

## Comment 4

> Username: stephengtuggy  
> Created at: 2023-01-09 00:24:58 UTC  
> Url: https://github.com/boostorg/python/issues/405#issuecomment-1374973776  

I see. I had `BOOST_DEBUG_PYTHON` in place, but not `Py_DEBUG`. Also, I was setting the compile definitions on the directory level, not per-target. Not sure if the latter makes a significant difference or not.

---

## Comment 5

> Username: stephengtuggy  
> Created at: 2023-01-16 01:53:35 UTC  
> Url: https://github.com/boostorg/python/issues/405#issuecomment-1383348689  

Closing, since @Osyotr 's solution worked. Thanks!

---

## Comment 6

> Username: stephengtuggy  
> Created at: 2024-12-11 03:04:45 UTC  
> Url: https://github.com/boostorg/python/issues/405#issuecomment-2533521642  

@Osyotr I linked your solution in my answer to my own StackOverflow question, here: https://stackoverflow.com/a/79270374/5067822  
  
Thanks again!
