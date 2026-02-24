# #481 - Unable to build Boost (1.71.0) with Python when Python is built with PyMalloc [Open]

> Username: thehcma  
> Created at: 2019-09-17 13:20:00 UTC  
> Updated at: 2022-01-18 19:04:13 UTC  
> Url: https://github.com/boostorg/build/issues/481  

When Python is built from source (my own testing was done with Python 3.7.4), the default is for it to build with PyMalloc (https://docs.python.org/3/c-api/memory.html#pymalloc).  
  
The implication for the Boost build is that there are two issues with that:  
  
* the include directory will be named (.../include/python3.7m)  
* the libraries will be named as libpython3.7m.*  
  
Note that in these two 2 cases there is an "m" suffix which throws off the Boost build as its unable to locate the proper include subdirectory and the library  
  
There is similar issue open in the Python Boost subproject, but I believe this is purely a build issue: https://github.com/boostorg/python/issues/41.  
  
IMO, a simple solution would be to accept the "m" suffix when specifying the Python that is to be used by Boost, something like:  
  
`using python : 3.7m : "/usr/local/python-3.7.4" : "/usr/local/python-3.7.4/include" : "/usr/local/python-3.7.4/lib" ;`

---

## Comment 1

> Username: thehcma  
> Created at: 2019-09-17 13:41:14 UTC  
> Updated at: 2019-09-17 14:28:38 UTC  
> Url: https://github.com/boostorg/build/issues/481#issuecomment-532226038  

Btw, here is how to programmatically get the include path (and thus figure out whether Python was built with PyMalloc or not):  
  
`python -c "import sys; import sysconfig; sys.stdout.write(sysconfig.get_path('include'))"`

---

## Comment 2

> Username: kc9jud  
> Created at: 2020-07-30 05:13:54 UTC  
> Url: https://github.com/boostorg/build/issues/481#issuecomment-666118694  

This problem seems to persist in 1.73.0.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:56 UTC  
> Url: https://github.com/boostorg/build/issues/481#issuecomment-850868242  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 4

> Username: adeadman  
> Created at: 2022-01-18 19:04:12 UTC  
> Url: https://github.com/boostorg/build/issues/481#issuecomment-1015732906  

This is still an issue. It appears the bootstrap populates the project-config.jam with just the path to the Python location, with the assumption that if we have this entry:  
  
```  
{  
    using python : 3.7 : "/path/to/Python/3.7.4"  
}  
```  
  
Then the headers will be at `/path/to/Python/3.7.4/include/python3.7` - which is not the case when compiled with PyMalloc. Instead, the bootstrapper should use the method outlined by thehcma to populate the include paths for the build script.
