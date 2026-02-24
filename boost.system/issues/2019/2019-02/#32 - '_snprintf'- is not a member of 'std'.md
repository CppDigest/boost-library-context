# #32 - '_snprintf': is not a member of 'std' [Closed]

> Username: babzog  
> Created at: 2019-02-11 21:51:05 UTC  
> Updated at: 2020-12-23 06:18:27 UTC  
> Closed at: 2019-02-12 20:16:42 UTC  
> Url: https://github.com/boostorg/system/issues/32  

While building our project with Visual Studio 2017 and linking to the new boost 1.69, I encountered the following error:  
  
> c:/git/oss/boost/1.69.0\boost/system/detail/system_category_win32.hpp(52): error C2039: '_snprintf': is not a member of 'std'C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.16.27023\include\functional(23): note: see declaration of 'std'  
> make.exe[2]: *** [c:/artifacts/build32//client//core/obj/buttercup.obj] Error 2  
  
I don't actually know what series of header #includes led to system_category_win32.hpp being yanked in, but it is... and it built without error when we were using boost 1.51.  
  
Googling didn't really help explain very well, why this is happening.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-02-11 22:03:21 UTC  
> Url: https://github.com/boostorg/system/issues/32#issuecomment-462512991  

You have `#define snprintf _snprintf` somewhere before including the Boost.System header.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-02-11 22:04:31 UTC  
> Url: https://github.com/boostorg/system/issues/32#issuecomment-462513345  

(This #define should no longer be necessary for VS2015 and above - it has a legitimate `snprintf`.)

---

## Comment 3

> Username: bstaletic  
> Created at: 2019-02-12 20:01:07 UTC  
> Url: https://github.com/boostorg/system/issues/32#issuecomment-462912013  

I had this problem. `Python.h` does the following  
  
```c++  
#ifndef HAVE_SNPRINTF  
#define snprintf _snprintf  
#endif   
```  
  
You just need to add `/DHAVE_SNPRINTF` to your flags.

---

## Comment 4

> Username: babzog  
> Created at: 2019-02-12 20:17:08 UTC  
> Url: https://github.com/boostorg/system/issues/32#issuecomment-462917512  

That was indeed my problem. That declaration was littered throughout the code. Thank you for the guidance!

---

## Comment 5

> Username: omeryasar  
> Created at: 2020-12-20 11:27:13 UTC  
> Url: https://github.com/boostorg/system/issues/32#issuecomment-748594988  

> ```c++  
> #ifndef HAVE_SNPRINTF  
> #define snprintf _snprintf  
> #endif   
> ```  
  
Hi, I have the same problem but I don't understand exactly what should I have to do for fixing the problem. Can you please explain your answer

---

## Comment 6

> Username: kedakedaxia  
> Created at: 2020-12-23 06:18:27 UTC  
> Url: https://github.com/boostorg/system/issues/32#issuecomment-749960263  

you can Add to header file "#undef snprintf"
