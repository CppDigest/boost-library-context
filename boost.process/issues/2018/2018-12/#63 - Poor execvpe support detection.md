# #63 - Poor execvpe support detection [Closed]

> Username: reddwarf69  
> Created at: 2018-12-27 14:50:15 UTC  
> Updated at: 2019-04-09 06:34:02 UTC  
> Closed at: 2019-04-09 06:34:02 UTC  
> Url: https://github.com/boostorg/process/issues/63  

Because of https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/executor.hpp#L35 boost::process fails to build with uClibc.  
  
While at some point it was added (https://git.uclibc.org/uClibc/commit/?id=0eb30761a26c46aaf555464114851202ae9c27bd), that was after the latest release (0.9.33.2).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-01-07 10:41:03 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-451893892  

Thanks for the report - I am not really sure now. Does boost.process wrongfully assumes `execvpe` exists or does it not use if it exists? And how should the detection look?

---

## Comment 2

> Username: reddwarf69  
> Created at: 2019-01-07 11:23:21 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-451904673  

It supposes that if `__GLIBC__` is defined then the system already provides execvpe(). It's probably certainly true that any glibc version still in use does provide execvpe(). But the check is not precise enough:  
  
- There may be other C libraries other than glibc providing execvpe(), but not defining `__GLIBC__`. IIRC musl refuses to define `__GLIBC__`, and it may actually be defining execvpe(). If I am right, Boost.Process is unnecessarily building its own version of execvpe() when using musl (e.g. current OpenWRT versions).  
  
- There are libraries other than glibc that do define `__GLIBC__`, but may not provide execvpe(). They do this precisely because apps often do like this, and instead of detecting the presence of the feature they just check for the presence of glibc. One specific example of this is any stable release of uClibc. The latest versions of OpenWRT still using uClibc did patch it to include the support, but unpatched versions of uClibc are still in use in lots of embedded devices (for no good reason, but it's a fact I can't change).  
  
Sorry, I don't really know the Boost build system, so I can't provide a patch to check specifically for execvpe().

---

## Comment 3

> Username: reddwarf69  
> Created at: 2019-01-07 15:06:31 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-451964372  

FWIW Boost.Thread needs to check if there is `lockfree boost::atomic_flag` and it checks for the specific feature in https://github.com/boostorg/thread/tree/develop/build.  
  
There is a `has_atomic_flag_lockfree_test.cpp` file to do the check itself and in Jamfile.v2 there are a few references to it.  
  
I still don't know anything about the Boost build system and, honestly, I don't want to know. But I guess Boost.Process needs something similar.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2019-01-08 03:47:12 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-452165628  

It's not build system issue, process is head only. I just need to have the proper ' #ifdef' to check that. Will see if I can find a solution there.

---

## Comment 5

> Username: reddwarf69  
> Created at: 2019-01-08 09:31:12 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-452232106  

Again, no idea how things are done in Boost. But I expect any attempt to check for the feature using only headers to fail in some corner case.  
  
Even if doing a header only library, in other projects I would expect the build system to detect if the feature is supported by trying to actually compile/run with it. Then the build system would maybe create a `config.h` header with something like  
```  
#ifndef BOOST_PROCESS_HAS_EXECVPE  
#define BOOST_PROCESS_HAS_EXECVPE 1  
#endif  
```  
(the `1` could be a `0` if the build system finds that the feature is not supported)  
to be installed with the rest of the header only library. Other parts of the header only library would `#include "config.h"` to know what the build system has found.  
  
Looking at other header only Boost libraries... Asio seems to do its best to do the detection using only the headers. But I guess knowing that the detection will be imperfect, it puts everything inside a `#if !defined(BOOST_ASIO_HAS_GETADDRINFO)` so the user can override the detection.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2019-01-08 10:14:03 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-452245285  

I also have a header-only detection going, no `boost.config` required. I would also like to keep it that way. Maybe `__GLIBC__` + `not uCLibC` or something.

---

## Comment 7

> Username: reddwarf69  
> Created at: 2019-01-08 10:30:05 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-452250161  

`__GLIBC__` + `not uClibC` would fix my specific issue in the systems I currently have in front on me.  
  
...it would break the systems of people with a patched uClibc, and I guess any version of uClibc-ng https://www.uclibc-ng.org/.  
  
What about musl? It refuses to identify itself -> https://wiki.musl-libc.org/faq.html#Q:-Why-is-there-no-%3Ccode%3E__MUSL__%3C/code%3E-macro?  
  
I don't even know what Android's Bionic (https://en.wikipedia.org/wiki/Bionic_%28software%29) does.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2019-04-09 06:34:02 UTC  
> Url: https://github.com/boostorg/process/issues/63#issuecomment-481121168  

FWIW: I removed usage of execvpe and replaced it with my own solution. Will solve that issue and be in master soon.
