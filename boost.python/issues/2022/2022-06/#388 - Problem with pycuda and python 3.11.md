# #388 - Problem with pycuda and python 3.11 [Open]

> Username: easybob95  
> Created at: 2022-06-30 17:27:39 UTC  
> Updated at: 2022-10-28 16:23:29 UTC  
> Url: https://github.com/boostorg/python/issues/388  

Hello,  
  
in order to test Python 3.11 with pycuda, i have just installed latest pycuda version : 2022.1  
  
I use CUDA 11.6.2 with a windows 11 laptop.  
  
When i try my program (using pycuda), i get this error message :  
  
Traceback (most recent call last):  
  File "D:\Alain\Astro\Soft\PC\PC_Traitement_Video_V7_51g.py", line 19, in <module>  
    import pycuda.driver as drv  
  File "C:\Users\apail\AppData\Local\Programs\Python\Python311\Lib\site-packages\pycuda\driver.py", line 65, in <module>  
    from pycuda._driver import *  # noqa  
SystemError: type Boost.Python.enum has the Py_TPFLAGS_HAVE_GC flag but has no traverse function  
  
I also tried with boost.python 1.78 but i get the same issue.  
  
Any help will be appreciate.  
  
Alain

---

## Comment 1

> Username: inducer  
> Created at: 2022-06-30 19:00:45 UTC  
> Url: https://github.com/boostorg/python/issues/388#issuecomment-1171570412  

There is some possible context for the error here, particular in regard to changed requirements in Py 3.11: https://github.com/wjakob/nanobind/pull/22#discussion_r846382901

---

## Comment 2

> Username: berolinux  
> Created at: 2022-09-12 22:43:44 UTC  
> Url: https://github.com/boostorg/python/issues/388#issuecomment-1244647012  

https://github.com/boostorg/python/pull/385

---

## Comment 3

> Username: AlainPaillou  
> Created at: 2022-10-26 12:39:25 UTC  
> Url: https://github.com/boostorg/python/issues/388#issuecomment-1291971975  

Hello,  
  
to tell the truth, i am not able to understand a single word in your responses. I am a very basic user ao Python and Pycuda.  
  
Does your answers mean this issue will be solved in near future ?  
  
Alain

---

## Comment 4

> Username: jakirkham  
> Created at: 2022-10-28 16:10:59 UTC  
> Url: https://github.com/boostorg/python/issues/388#issuecomment-1295181981  

It means there is a fix in the `develop` branch, but IIUC not in a release yet. So should be fixed in the next release.

---

## Comment 5

> Username: AlainPaillou  
> Created at: 2022-10-28 16:23:29 UTC  
> Url: https://github.com/boostorg/python/issues/388#issuecomment-1295194645  

Hello jakirkham,  
  
many thx for your response. Quite a good news !  
  
Alain
