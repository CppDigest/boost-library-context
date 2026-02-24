# #191 permessage-deflate extension for WebSocket [Closed]

> Username: vinniefalco  
> Created at: 2016-11-16 16:09:13 UTC  
> Updated at: 2017-07-25 17:44:17 UTC  
> Closed at: 2016-11-16 20:26:18 UTC  
> Url: https://github.com/boostorg/beast/pull/191  

This adds support for the permessage-deflate extension, documented here:  
https://tools.ietf.org/html/draft-ietf-hybi-permessage-compression-00  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/b21/index.html  
  
Autobahn|Testsuite results:  
http://vinniefalco.github.io/stage/autobahn/  
  
Reviewers: @HowardHinnant @ximinez

---

## Comment 1

> Username: HowardHinnant  
> Created_at: 2016-11-16 17:24:19 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261011704  

I'm just now noticing a waring coming out of test/zlib/zlib-1.2.8/inflate.c:1507:  
  
```  
 warning: shifting a negative signed value is undefined [-Wshift-negative-value]  
    if (strm == Z_NULL || strm->state == Z_NULL) return -1L << 16;  
                                                        ~~~ ^  
```  
  
I think this should be taken seriously, and it is easily fixed:  
  
```  
    if (strm == Z_NULL || strm->state == Z_NULL) return static_cast<long>(~0UL << 16);  
```  
  
I've tested this on all three compilers (online) and I'm seeing no warnings, and the correct constant, both for 32bit and 64bit long.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2016-11-16 17:26:28 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261012305  

The problem is then I am modifying the ZLib source code. If anything, that change needs to be submitted to the upstream. And note, the file is compiled with the C compiler.

---

## Comment 3

> Username: HowardHinnant  
> Created_at: 2016-11-16 17:33:00 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261014194  

Understood.  I don't think the ZLib source code is trustworthy without this fix.  Today's C++ compilers aggressively optimize code into non-intuitive behaviors in situations like this.  There is a very real possibility that any part of this TU (and the TU is even bigger with unity builds) does not do what is intended.  
  
That is, my recommendation is either to do whatever it takes to fix this, or not use the ZLib code.  A C-style cast to long instead of static_cast would be acceptable.

---

## Comment 4

> Username: HowardHinnant  
> Created_at: 2016-11-16 17:37:48 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261015484  

Here's a simpler fix that doesn't require a cast:  
  
```  
 if (strm == Z_NULL || strm->state == Z_NULL) return ~0xFFFFL;  
```

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-11-16 17:45:14 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261017635  

hmm... looks like it was fixed:  
https://github.com/madler/zlib/issues/111

---

## Comment 6

> Username: HowardHinnant  
> Created_at: 2016-11-16 18:20:49 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261027447  

Their fix looks like a rant (one which I agree with) :-).  At least they are aware of the issue.  
  
I ran a godbolt test and was unable to find a compiler/version that optimized based on the theory that UB can not happen (which is good).

---

## Comment 7

> Username: codecov-io  
> Created_at: 2016-11-16 18:40:53 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261033251  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/191?src=pr) is 90.58% (diff: 50.49%)  
  
> Merging [#191](https://codecov.io/gh/vinniefalco/Beast/pull/191?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **5.91%**  
  
``` diff  
@@             master       #191   diff @@  
==========================================  
  Files            83         85     +2     
  Lines          5746       6192   +446     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
+ Hits           5545       5609    +64     
- Misses          201        583   +382     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [62e65ed...a285142](https://codecov.io/gh/vinniefalco/Beast/compare/62e65ed5e2f4c89564d8afd1050ab82fc03cab3b...a285142ae02d30f38e7e664e4c4832d6592df206?src=pr)

---

## Comment 8

> Username: HowardHinnant  
> Created_at: 2016-11-16 19:44:52 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261050684  

`threading-multi/zlib-tests` has been running for 16min now...

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-11-16 20:26:29 UTC  
> Url: https://github.com/boostorg/beast/pull/191#issuecomment-261061597  

Dealing with zlib test failures, will re-open

---
