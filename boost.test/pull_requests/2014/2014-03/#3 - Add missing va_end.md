# #3 Add missing va_end [Closed]

> Username: gjasny  
> Created at: 2014-03-15 09:58:52 UTC  
> Updated at: 2014-07-18 10:38:42 UTC  
> Closed at: 2014-05-01 08:43:46 UTC  
> Url: https://github.com/boostorg/test/pull/3  

There is an unbalanced va_start / va_end sequence in debug.ipp  
  
This fixes https://svn.boost.org/trac/boost/ticket/9779  
  
Signed-off-by: Gregor Jasny gjasny@googlemail.com

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2014-05-01 08:43:44 UTC  
> Url: https://github.com/boostorg/test/pull/3#issuecomment-41892152  

I forgot to leave a message. This is merged into develop (ticket updated).  
  
Thanks!  
Raffi

---

## Comment 2

> Username: Belcourt  
> Created_at: 2014-05-01 22:10:16 UTC  
> Url: https://github.com/boostorg/test/pull/3#issuecomment-41962848  

Before pushing this change to release, I'd have suggested you first fix it on develop, then wait for the tests to cycle and then merge to release.  This same problem exists on develop, care to submit a pull request against develop?

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2014-05-01 22:33:57 UTC  
> Url: https://github.com/boostorg/test/pull/3#issuecomment-41964755  

This is pushed onto the branch "tractickets/9779". The tests do not pass on my own setup on develop, this is why the merge is postponed until I am done with that. Then the fix will be merged onto develop and of course onto master.  
  
However, it would have been much easier to be able to run the tests on a branch from master (or a branch from master + this patch). I do currently not know how to do that.

---
