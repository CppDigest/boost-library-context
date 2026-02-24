# #623 - While building --includedir parameter is ignored [Open]

> Username: randre70  
> Created at: 2020-06-12 12:23:02 UTC  
> Updated at: 2021-05-29 17:22:32 UTC  
> Url: https://github.com/boostorg/build/issues/623  

Resulting boost header files folder is always created inside BOOST_ROOT

---

## Comment 1

> Username: randre70  
> Created at: 2020-06-12 12:26:28 UTC  
> Url: https://github.com/boostorg/build/issues/623#issuecomment-643243835  

BOOST_ROOT is /home/me/Documents/boost  
--build-dir is /home/me/Documents/_build  
--includedir is /home/me/Documents/_build/include  
--libdir is /home/me/Documents/_build/lib  
--stagedir is /home/me/Documents/_build/stage  
  
```bash  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    /home/me/Documents/boost  
  
The following directory should be added to linker library paths:  
  
    /home/me/Documents/_build/stage/lib  
  
```

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:02 UTC  
> Url: https://github.com/boostorg/build/issues/623#issuecomment-850868261  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
