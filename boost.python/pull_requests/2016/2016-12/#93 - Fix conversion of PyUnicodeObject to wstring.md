# #93 Fix conversion of PyUnicodeObject to wstring [Merged]

> Username: tadeu  
> Created at: 2016-12-13 21:27:37 UTC  
> Updated at: 2016-12-14 12:58:27 UTC  
> Merged at: 2016-12-14 12:58:07 UTC  
> Closed at: 2016-12-14 12:58:07 UTC  
> Url: https://github.com/boostorg/python/pull/93  

On Windows, with Python >= 3.3, `PyObject_Length` cannot be used to get  
the size of the `wchar_t` string, because it will count the number of  
*code points*, but some characters not on the BMP will use two UTF-16  
*code units* (surrogate pairs).  
This is not a problem on Unix, since `wchar_t` is 32-bit.  
  
This also fixes a problem where `test_builtin_converters.py` was not  
being run, since the module docstring was not the first statement.

---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2016-12-13 21:56:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/python/pull/93#pullrequestreview-12788510  

Hi Tadeu,  
  
thanks for the patch !  
May I ask for two changes:  
  
* please submit the fix for the test_builtin_converters.py test separately (with a message indicating the bug this fixes).  
  
* please add a comment to the conditional explaining why PyObject_Length doesn't work (in that case).  
(Just putting the explanation from this pull request itself is fine. The point is to let people reviewing the code understand why this is needed.)  
  
Thanks !

---

## Comment 2

> Username: tadeu  
> Created_at: 2016-12-14 11:29:33 UTC  
> Url: https://github.com/boostorg/python/pull/93#issuecomment-267011326  

@stefanseefeld Of course, I've just made the changes, thanks!

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2016-12-14 12:58:27 UTC  
> Url: https://github.com/boostorg/python/pull/93#issuecomment-267027416  

Many thanks !

---
