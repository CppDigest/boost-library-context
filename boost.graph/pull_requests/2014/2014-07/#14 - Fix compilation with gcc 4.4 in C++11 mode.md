# #14 Fix compilation with gcc 4.4 in C++11 mode [Merged]

> Username: Lastique  
> Created at: 2014-07-30 08:03:57 UTC  
> Updated at: 2014-08-04 00:17:08 UTC  
> Merged at: 2014-07-30 21:13:56 UTC  
> Closed at: 2014-07-30 21:13:56 UTC  
> Url: https://github.com/boostorg/graph/pull/14  

Add constructor and assignment operator implementations for gcc 4.4 since it does not support defaulted move constructors and assignment. The operators are also used for gcc 4.5 for good measure (I cannot test it but gcc 4.6 does not need this workaround).  
  
Also the workaround is used for MSVC as well. The previous MSVC branch was incorrect since it did not invoke base class constructors and assignment.  
  
See the "[1.56.0] Release candidates available" discussion on the ML for the original compilation error.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2014-07-30 18:38:41 UTC  
> Updated_at: 2014-07-30 19:37:58 UTC  
> Url: https://github.com/boostorg/graph/pull/14#discussion_r15603367  

This looks extremely fishy to me. Why are you mutating a const value here?

---

## Comment 2

> Username: ericniebler  
> Created_at: 2014-07-30 18:39:12 UTC  
> Updated_at: 2014-07-30 19:37:58 UTC  
> Url: https://github.com/boostorg/graph/pull/14#discussion_r15603393  

And why are you stealing resources from an lvalue?

---

## Comment 3

> Username: Lastique  
> Created_at: 2014-07-30 18:47:03 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-50661484  

I have followed the original MSVC code, which was doing that. I don't know Boost.Graph well enough to tell if that code was intentionally written that way or if that was a yet another bug. I agree that the behavior looked strange though.  
  
I can change the patch to exclude the lvalue constructor and operator. We can merge it to develop to see how it goes. (I will run the tests locally as well, of course.)

---

## Comment 4

> Username: ericniebler  
> Created_at: 2014-07-30 19:09:25 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-50664512  

Looks like we've been stealing the guts from lvalues since 2000:  
boostorg/graph@fd7f5d5a0eb7d01bf1e53f6838dcf5b082a3458d  
  
I'm guessing this has something to do with C++98's inability to distinguish between rvalues and const lvalues, together with the strangeness of `std::auto_ptr`. In other words, with `std::unique_ptr` and rvalue refs, we can and probably should be doing better here. What breaks if you remove the lvalue constructors/assignment operators? I hate doing this kind of surgery this close to release.  
  
Also, please review the patch here: https://svn.boost.org/trac/boost/ticket/10165. It looks like this code is broken in yet another interesting way. &lt;sigh&gt;

---

## Comment 5

> Username: Lastique  
> Created_at: 2014-07-30 19:42:01 UTC  
> Updated_at: 2014-07-30 19:44:39 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-50668831  

Unfortunately, the tests fail to compile with gcc 4.4 and the lvalue stuff removed. The compiler tries to generate the copy constructor despite the fact that the move constructor is present. It fails because unique_ptr is not copyable. I'm not sure if that is also the case with MSVC, I can't test it now. I did have to add a small fix to resolve the assignment operator ambiguity; otherwise the tests failed as well.  
  
Looking at the code, it is now very much similar to the auto_ptr-based one. We might just as well use auto_ptr branch for all compilers that don't support defaulted move functions. Although gcc would emit warnings about "deprecated" auto_ptr in C++11 mode. Perhaps this was the motivation behind this code.  
  
> Also, please review the patch here: https://svn.boost.org/trac/boost/ticket/10165. It looks like this code is broken in yet another interesting way.  
  
Yes, that bug should be fixed by this pull request. I haven't tested though.

---

## Comment 6

> Username: ericniebler  
> Created_at: 2014-07-30 21:13:53 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-50680854  

OK, merging as-is into develop and hoping for the best.

---

## Comment 7

> Username: ericniebler  
> Created_at: 2014-07-30 21:20:54 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-50681696  

FYI, I confirmed that https://svn.boost.org/trac/boost/ticket/10165 was fixed by this change.

---

## Comment 8

> Username: Lastique  
> Created_at: 2014-07-30 22:35:29 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-50689906  

Thanks, Eric.

---

## Comment 9

> Username: Lastique  
> Created_at: 2014-08-04 00:15:23 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-51007912  

It seems the tests are green. Could this be merged to master?

---

## Comment 10

> Username: Lastique  
> Created_at: 2014-08-04 00:17:08 UTC  
> Url: https://github.com/boostorg/graph/pull/14#issuecomment-51007960  

Nevermind, I can see it's already there.

---
