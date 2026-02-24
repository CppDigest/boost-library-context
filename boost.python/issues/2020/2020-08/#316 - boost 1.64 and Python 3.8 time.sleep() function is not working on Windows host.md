# #316 - boost 1.64 and Python 3.8 time.sleep() function is not working on Windows host. [Closed]

> Username: mbilal100  
> Created at: 2020-08-25 14:53:20 UTC  
> Updated at: 2020-09-01 14:30:40 UTC  
> Closed at: 2020-09-01 14:30:40 UTC  
> Url: https://github.com/boostorg/python/issues/316  

Hi,  
  
I'm seeing very odd behavior of boost python3 on **Windows host** where `time.sleep()` function is not working.  
We're extending and embedding Python 3.8.2 with boost 1.64. Note, there is no issue found for Linux host.  
  
I have tested and confirmed that other Python time class function (i.e `time.time() `or `time.ctime()` etc ) are still be working fine but not `time.sleep()`  , it is also confirmed that stand alone Python 3 time.sleep() is working on Windows.  
  
We have already successfully embedded, extended and using the python 2.7 with boost 1.64 for both Linux and Windows host, but because of  Python 2 has now EOF that's why we are moving on Python 3.  
  
Following are example output from Python script on both host.  
  
```  
time.sleep(5)  
print("elapsed: %d" % (time.time() - start))  
```  
When I run that on Linux I get correct:  
  
```  
 elapsed: 5  
```  
But on Windows, I get:  
  
```  
elapsed: 0  
```  
  
  
  
Thanks,  
Muhammad Bilal  
Mentor, A Simens Business.   
muhammad_bilal@mentor.com

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2020-09-01 13:34:50 UTC  
> Url: https://github.com/boostorg/python/issues/316#issuecomment-684857642  

Hi @mbilal100 , can you please provide a complete (but minimal) self-contained test exhibiting the error ? The code snippets above don't seem to involve `Boost.Python` at all, so it isn't quite clear to me how the two would interact to cause this behaviour.

---

## Comment 2

> Username: mbilal100  
> Created at: 2020-09-01 14:30:40 UTC  
> Url: https://github.com/boostorg/python/issues/316#issuecomment-684897346  

Hi @stefanseefeld   
  
Sorry for the late reply, it is actually not a boost python issue but coming from the main Python C API.  
see https://bugs.python.org/issue41686
