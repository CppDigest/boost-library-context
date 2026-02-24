# #522 Support python3 in building documentation [Merged]

> Username: vissarion  
> Created at: 2018-10-24 09:07:52 UTC  
> Updated at: 2019-07-03 09:28:17 UTC  
> Merged at: 2018-11-14 13:40:26 UTC  
> Closed at: 2018-11-14 13:40:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/522  

Python 3 removed the global `execfile` function (see https://docs.python.org/3.5/whatsnew/3.0.html?highlight=execfile#builtins) and thus `make_qbk.py` script produces the error `name 'execfile' is not defined` (even with `Python 2.7.6` in my case).  
  
This PR proposes a fix (tested with `Python 2.7.6` and `Python 3.4.3`).

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2018-10-24 09:18:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/522#pullrequestreview-167807042  

📁 doc/make_qbk.py

```diff
 201 | os.chdir("index")
 201 |- execfile("make_qbk.py")
 202 |+ exec(compile(open("make_qbk.py", "rb").read(), "make_qbk.py", 'exec'))
```

> Username: mloskot  
> Created_at: 2018-10-24 09:18:13 UTC  
> Updated_at: 2018-10-24 09:18:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/522#discussion_r227706675  

Wouldn't it be sufficient to call `exec(open("make_qbk.py").read())`?  
  
As per https://docs.python.org/3.3/whatsnew/3.0.html#builtins  
> Removed execfile(). Instead of execfile(fn) use exec(open(fn).read()).  
  
Generally, catching up with Python 3 is a good idea, I think.

> Username: vissarion  
> Created_at: 2018-10-24 09:35:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/522#discussion_r227714236  

I used the call used in `2to3` script; see https://github.com/python/cpython/blob/d4d60134b29290049e28df54f23493de4f1824b6/Lib/lib2to3/fixes/fix_execfile.py   
As far as I understand, that call has at least better error handling.

> Username: mloskot  
> Created_at: 2018-10-24 10:12:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/522#discussion_r227726906  

Better errors handling is not entirely clear to me. Perhaps due to the fact `exec` and `compile` make two step action.  
I've never bothered to use 2to3 really. Unless Python 2 support is desired, I'd stick to plain Python 3 API. Anyhow, I just wondered.


---
