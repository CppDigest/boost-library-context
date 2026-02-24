# #173 - Missing __attribute__ ((__visibility__("default"))) with Clang on FreeBSD 11.1 [Closed]

> Username: thelfer  
> Created at: 2017-11-05 09:42:49 UTC  
> Updated at: 2017-11-05 15:35:19 UTC  
> Closed at: 2017-11-05 15:35:19 UTC  
> Url: https://github.com/boostorg/python/issues/173  

Dear Boost.Python developpers,  
  
First of all, thank you for your wonderfull work. I am using boost.python for several years with great satisfaction.  
  
The issue is mostly described in the title: if we use the flag `-fvisibility=hidden`, the "init" function of a module is not visible and thus can not be loaded in `python`.  
  
The issue is not present on Linux and gcc, because  __attribute__ ((__visibility__("default"))) is added to the declaration of this function.  
  
A quick and dirty workaround is to compile the code with the BOOST_PYTHON_USE_GCC_SYMBOL_VISIBILITY flag (see boost/python/config/detail.hpp).  
  
I hope this helps.  
  
Best regards,  
  
Thomas Helfer

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-11-05 14:33:36 UTC  
> Url: https://github.com/boostorg/python/issues/173#issuecomment-341977499  

Hi Thomas,  
I believe I have merged a fix for this two weeks ago: https://github.com/boostorg/python/pull/159 so the current develop branch should work for you. Please confirm !

---

## Comment 2

> Username: thelfer  
> Created at: 2017-11-05 15:35:19 UTC  
> Url: https://github.com/boostorg/python/issues/173#issuecomment-341981909  

Hi Stefan,  
Indeed, this does the trick. Sorry for the duplicate, I shall have been more cautious.  
Best regards,  
Thomas
