# #280 - Handling errors inside BOOST_PYTHON_MODULE() [Closed]

> Username: db4  
> Created at: 2019-12-09 08:02:59 UTC  
> Updated at: 2021-02-08 04:07:31 UTC  
> Closed at: 2021-02-08 04:07:31 UTC  
> Url: https://github.com/boostorg/python/issues/280  

I have a module that may fail to initialize due to missing dependencies. So I do  
```c++  
BOOST_PYTHON_MODULE(mymodule)  
{  
...  
    if (missing_dependency) {  
        throw std::exception("Missing dependency");  
    }  
...  
}  
```  
The exception is correctly caught and `PyErr_SetString(PyExc_RuntimeError, x.what())` is called, but python reports  
```  
    import mymodule  
SystemError: initialization of mymodule raised unreported exception  
```  
Why `unreported exception`? My platform is Windows 10/Python 3.7.4 x64/MSVC 2017 if that matters. python_boost version is 1.69.0.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-12-09 12:51:56 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-563224464  

What happens if you replace `std::exception` by `std::runtime_error` or some other more specific exception type ?

---

## Comment 2

> Username: db4  
> Created at: 2019-12-09 14:00:18 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-563250961  

No, switching to `std::runtime_error` does not help, I still have `unreported exception`. And looks like the exception type has nothing to do with the issue: I set a debug breakpoint in  
https://github.com/boostorg/python/blob/ac62db1cf17c0af44dc2b2117f9ddeee327e5e7c/src/errors.cpp#L48-L51  
and verified that this catch case was executed.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2019-12-09 14:04:37 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-563252737  

OK, I see. So the Python error has correctly been set by Boost.Python, but Python's own runtime then flags this as an "unreported exception". In other words, you could strip your code off all Boost.Python references, only using Python's own C API, and you'd still observe the issue.  
Can you report this to the Python bug tracker ?

---

## Comment 4

> Username: db4  
> Created at: 2019-12-09 14:10:08 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-563254907  

Are you sure that's a Python problem? Maybe  
https://github.com/boostorg/python/blob/1727b67a5a92c8c3564d4ee244fffd758e54d7cc/src/module.cpp#L24  
should be replaced with  
```c++  
if (handle_exception(init_function)) return NULL;  
```  
?

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2019-12-09 14:23:41 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-563261073  

Oh, indeed, that looks sensible. Can you write a (minimal) test case to demonstrate the problem as well as that your proposed change fixes it ? That would make a great PR !

---

## Comment 6

> Username: db4  
> Created at: 2019-12-09 14:31:29 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-563264573  

I just tried to fix it locally this way and it solved the problem. Well, I could try to create a test case and PR, but I'm quite new to boost libs testing infrastructure...

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2019-12-09 14:35:06 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-563266169  

OK, great. I'll see whether I can write a quick test.

---

## Comment 8

> Username: db4  
> Created at: 2020-06-29 07:29:40 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-650984353  

Hi @stefanseefeld  
Any news on the issue? Are you still planning to create a test and finally commit the fix?

---

## Comment 9

> Username: tdelame  
> Created at: 2020-11-20 14:18:20 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-731196606  

Had the same issue, boost::python was hiding the real exception with `raised unreported exception`. I applied the patch `if (handle_exception(init_function)) return NULL;` mentioned by @db4 and now the real exception is thrown.

---

## Comment 10

> Username: twvd  
> Created at: 2021-02-02 08:56:06 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-771476965  

Also just ran into this problem.. Just bumping this issue to show it's still relevant and affects users.

---

## Comment 11

> Username: db4  
> Created at: 2021-02-03 11:21:06 UTC  
> Url: https://github.com/boostorg/python/issues/280#issuecomment-772435004  

> Also just ran into this problem.. Just bumping this issue to show it's still relevant and affects users.  
  
I finally decided to stop waiting for something and create PR myself
