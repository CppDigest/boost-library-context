# #278 configure VxWorks/Dinkum STL to match <rtti>/<exception-handling> [Closed]

> Username: kuhlenough  
> Created at: 2018-01-12 05:24:03 UTC  
> Updated at: 2021-10-02 22:08:19 UTC  
> Closed at: 2018-01-25 17:24:30 UTC  
> Url: https://github.com/boostorg/build/pull/278  



---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-01-12 17:54:18 UTC  
> Url: https://github.com/boostorg/build/pull/278#issuecomment-357308845  

AMDG  
  
This needs a test case to make sure that it stays working.  
- Add test/toolset_xxx.py  
- Add test/toolset-mock/src/xxx.py  
- Add the toolset to test/toolset-mock/project-config.jam  
  
Note: I can write the tests myself.  It'll just take a  
little longer, and I don't have access to this compiler,  
so I can't easily verify that it's actually right.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: kuhlenough  
> Created_at: 2018-01-16 19:48:44 UTC  
> Url: https://github.com/boostorg/build/pull/278#issuecomment-358082969  

My goal is to have server contribute VxWorks build results to http://www.boost.org/development/tests/master/developer/summary.html  
In my current involved VxWorks environment none of the <rtti> and <exception-handling> tests passed till I made this change.

---
