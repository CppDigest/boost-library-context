# #8 eliminate unnecessary workaround for modern msvc versions [Merged]

> Username: ericniebler  
> Created at: 2014-06-27 05:39:24 UTC  
> Updated at: 2014-06-27 11:42:17 UTC  
> Merged at: 2014-06-27 11:42:17 UTC  
> Closed at: 2014-06-27 11:42:17 UTC  
> Url: https://github.com/boostorg/mpl/pull/8  

There is a very old workaround for MSVC in has_xxx.hpp that is no longer needed. It is now causing problems for Boost.Range (see https://github.com/boostorg/range/pull/11 for a full discussion of the problem).  
  
I did some digging into the reason for the workaround, and believe it is in response to the following ancient bug: [#1317](https://svn.boost.org/trac/boost/ticket/1317). I can confirm that the test case in that bug report indeed fails without the workaround on msvc 8. However, in more recent versions, the workaround is unnecessary.  
  
I have run mpl's full regression test suite with msvc 8, 9, 10, 11, and 12. No regressions. I have also tested the code in bug #1317 with the same compiler versions without any trouble.

---
