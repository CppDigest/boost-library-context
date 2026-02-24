# #202 Lex: Fixed unique_ptr usage [Merged]

> Username: Kojoley  
> Created at: 2016-08-14 22:07:46 UTC  
> Updated at: 2017-11-12 21:57:34 UTC  
> Merged at: 2016-08-14 22:21:00 UTC  
> Closed at: 2016-08-14 22:21:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/202  

Lex is using `unique_ptr` from `movelib` and relying on a bug in it, described in [a ticket](https://svn.boost.org/trac/boost/ticket/11758).  
Since that bug fixed in `movelib` Lex is uncompilable.  
  
I have simplified unique_ptr usage by the following rules:  
- `delete ptr.release()` is equal to `ptr.reset()`  
- `delete a.release(); a = b;` is equal to `b.swap(a); b.reset();`  
  
Now Lex test suite compiles and runs succesfully. VC++ 6 should be happy too despite the fact that Boost no longer supports it.

---

## Comment 1

> Username: hkaiser  
> Created_at: 2016-08-14 22:50:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/202#issuecomment-239702668  

@Kojoley thanks!

---

## Comment 2

> Username: mxc-commons  
> Created_at: 2016-08-15 06:08:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/202#issuecomment-239735239  

@Kojoley May The Force Be With You! :)

---
