# #480 - Don't require BOOST_ROOT and BOOST_INCLUDEDIR when installing boost with brew [Closed]

> Username: vinniefalco  
> Created at: 2017-06-13 04:49:15 UTC  
> Updated at: 2017-06-14 00:38:21 UTC  
> Closed at: 2017-06-14 00:38:21 UTC  
> Url: https://github.com/boostorg/beast/issues/480  

Why do these variables have to be set? Maybe they don't?

---

## Comment 1

> Username: xsacha  
> Created at: 2017-06-13 05:57:23 UTC  
> Updated at: 2017-06-13 05:58:39 UTC  
> Url: https://github.com/boostorg/beast/issues/480#issuecomment-308014727  

This is only because you install from a unzipped package rather than searching system.  
In fact, you have disabled system libraries from being found:  
https://github.com/vinniefalco/Beast/blob/master/CMakeLists.txt#L51  
  
I always disable these custom options before running locally.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-13 12:10:59 UTC  
> Url: https://github.com/boostorg/beast/issues/480#issuecomment-308096039  

Should I remove that line? What do you suggest to fix this?

---

## Comment 3

> Username: xsacha  
> Created at: 2017-06-13 13:18:54 UTC  
> Url: https://github.com/boostorg/beast/issues/480#issuecomment-308112733  

Those root and includedir are needed on jenkins for extracted boost.  
Otherwise just install libboost.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-13 13:40:45 UTC  
> Url: https://github.com/boostorg/beast/issues/480#issuecomment-308119299  

Setting this to `ON`  
```  
set (Boost_NO_SYSTEM_PATHS ON)  
```  
only skips searching system paths, it still looks in BOOST_ROOT and BOOST_INCLUDEDIR, see:  
https://lists.gnu.org/archive/html/discuss-gnuradio/2011-12/txta1SOQqu6kA.txt  
  
The question becomes then, why skip the system paths?

---

## Comment 5

> Username: xsacha  
> Created at: 2017-06-13 21:20:11 UTC  
> Url: https://github.com/boostorg/beast/issues/480#issuecomment-308252097  

Yeah I do not know why it was skipping system paths. I just remove it when I need to actually use it. I assumed there was a reaaon.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-13 21:35:47 UTC  
> Url: https://github.com/boostorg/beast/issues/480#issuecomment-308255758  

I'm not a cmake expert by any means I just keep hacking away at it until it works!  I must have copied that line from somewhere...
