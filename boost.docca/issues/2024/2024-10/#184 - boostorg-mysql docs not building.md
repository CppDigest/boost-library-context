# #184 - boostorg/mysql docs not building [Closed]

> Username: sdarwin  
> Created at: 2024-10-28 12:55:49 UTC  
> Updated at: 2024-10-31 11:25:52 UTC  
> Closed at: 2024-10-31 11:25:52 UTC  
> Url: https://github.com/boostorg/docca/issues/184  

boostorg/mysql docs are not building on macOS and Windows. Getting the following error.    
  
```  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/include/docca/quickbook/components.jinja2", line 498, in template  
    {%- for part in member.brief -%}  
^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/venvboostdocs/lib/python3.12/site-packages/jinja2/runtime.py", line 417, in __init__  
    self._iterator = self._to_iterator(iterable)  
                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/venvboostdocs/lib/python3.12/site-packages/jinja2/runtime.py", line 425, in _to_iterator  
    return iter(iterable)  
           ^^^^^^^^^^^^^^  
jinja2.exceptions.UndefinedError: '__main__.OverloadSet object' has no attribute 'brief'  
```  
  
See https://github.com/sdarwin/release-tools/actions/runs/11525770777/job/32088676007  
  
In the "docs" job, search for: DOCS BUILD FAILED. LIBRARY mysql , and scroll up.  
  
It's possible that a python dependency is missing, or a configuration issue, in which case there is nothing wrong with docca.  
Any ideas?  
  
cc: @anarthal

---

## Comment 1

> Username: anarthal  
> Created at: 2024-10-28 13:52:49 UTC  
> Url: https://github.com/boostorg/docca/issues/184#issuecomment-2441654246  

This looks like it's hitting a known docca issue. If that's the case, it should be failing for Linux, too. Can you confirm whether this is the case?  
  
Will look into it in a couple hours anyway.

---

## Comment 2

> Username: sdarwin  
> Created at: 2024-10-28 14:25:33 UTC  
> Url: https://github.com/boostorg/docca/issues/184#issuecomment-2441741966  

> , it should be failing for Linux, too  
  
It's succeeding on Linux.   Also, the main boost releases (on linux) are ok.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-10-28 14:43:55 UTC  
> Updated at: 2024-10-28 14:44:38 UTC  
> Url: https://github.com/boostorg/docca/issues/184#issuecomment-2441790886  

I'll look into this tomorrow. ~Is it failing on develop or master?~

---

## Comment 4

> Username: anarthal  
> Created at: 2024-10-28 15:00:19 UTC  
> Url: https://github.com/boostorg/docca/issues/184#issuecomment-2441837183  

It's hitting this docca issue: https://github.com/boostorg/docca/issues/170  
  
We don't see it in Linux because it doesn't happen with Doxygen 1.9, but it does with Doxygen 1.12.

---

## Comment 5

> Username: anarthal  
> Created at: 2024-10-28 15:03:12 UTC  
> Url: https://github.com/boostorg/docca/issues/184#issuecomment-2441844989  

IIRC the problem can be workarounded by removing the `\relates` commands from the documentation. This is not ideal, but can be an option if fixing this in docca is too complex to be achieved on time for the release. @grisumbras do you think this could be fixed on your side?

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-10-28 15:10:17 UTC  
> Url: https://github.com/boostorg/docca/issues/184#issuecomment-2441863637  

Yeah, I think I can do this in time.

---

## Comment 7

> Username: anarthal  
> Created at: 2024-10-28 15:47:05 UTC  
> Url: https://github.com/boostorg/docca/issues/184#issuecomment-2441960991  

Great, thanks.
