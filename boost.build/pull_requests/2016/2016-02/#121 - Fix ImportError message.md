# #121 Fix ImportError message. [Closed]

> Username: frenchtoast747  
> Created at: 2016-02-22 14:07:21 UTC  
> Updated at: 2021-10-02 22:19:00 UTC  
> Closed at: 2016-10-28 01:27:08 UTC  
> Url: https://github.com/boostorg/build/pull/121  

It's possible that when importing a module (that exists and is importable) the module itself contains an ImportError. This is caught by the exception handling and falsely reports that the module itself is not able to be found.  
  
This change makes sure that ImportError is referring to the module trying to be imported. If it's not, then the original ImportError is re-raised in order to inform the end-user correctly.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2016-02-26 03:25:52 UTC  
> Url: https://github.com/boostorg/build/pull/121#issuecomment-189095256  

AMDG  
  
  This seems rather fragile.  Is there any  
way to determine this without parsing the  
string?  Does python explicitly guarantee  
the format of the string?  This would fail  
if the error message could be localized,  
for instance.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: frenchtoast747  
> Created_at: 2016-02-29 16:23:59 UTC  
> Updated_at: 2016-02-29 16:25:19 UTC  
> Url: https://github.com/boostorg/build/pull/121#issuecomment-190276017  

You're right, this is terrible.  
  
Ideally, the module that is not importable should be set as an attribute on the `ImportError` instance, but it's not. There was [a patch for this functionality](https://bugs.python.org/issue1559549), but it's only found in Python 3.3+  
  
It might be possible to get the module that failed from the stack, but I think there might be some parsing involved with that as well.  
  
I'll continue research on this. Maybe the import order in `project.py` can be rearranged to let the `ImportError` fail on its own with the correct error message so that Boost.Build doesn't have to handle it at all.

---

## Comment 3

> Username: vprus  
> Created_at: 2016-10-26 09:04:34 UTC  
> Url: https://github.com/boostorg/build/pull/121#issuecomment-256290201  

Aaron, is this still outstanding? <br><br>:octocat: _Sent from [GH](http://nodegh.io)._

---

## Comment 4

> Username: frenchtoast747  
> Created_at: 2016-10-26 14:11:04 UTC  
> Url: https://github.com/boostorg/build/pull/121#issuecomment-256359234  

Yes, it still is. I did not include this change on the features/python-updates branch as it's still a nasty hack.

---
