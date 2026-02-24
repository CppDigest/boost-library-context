# #231 OpenBSD is missing SI_ASYNCIO and SI_MESGQ [Closed]

> Username: kernigh  
> Created at: 2019-10-04 00:21:38 UTC  
> Updated at: 2019-10-21 18:28:40 UTC  
> Closed at: 2019-10-21 18:28:34 UTC  
> Url: https://github.com/boostorg/test/pull/231  

Check if macros SI_ASYNCIO and SI_MESGQ are defined as macros.  This  
allows to run tests on OpenBSD 6.5, where the macros are missing.  
  
This is identical to [patch-boost_test_impl_execution_monitor_ipp](https://cvsweb.openbsd.org/cgi-bin/cvsweb/ports/devel/boost/patches/patch-boost_test_impl_execution_monitor_ipp?rev=1.3&content-type=text/x-cvsweb-markup) in  
OpenBSD Ports, except that I added a comment.

---

## Comment 1

> Username: kernigh  
> Created_at: 2019-10-04 00:25:58 UTC  
> Url: https://github.com/boostorg/test/pull/231#issuecomment-538179462  

This change assumes that system headers always define SI_ASYNCIO and SI_MESGQ as macros. This seems to be true in at least [FreeBSD](https://svnweb.freebsd.org/base/head/sys/sys/signal.h?revision=352807&view=markup#l408), [glibc](https://sourceware.org/git/?p=glibc.git;a=blob;f=sysdeps/unix/sysv/linux/bits/siginfo-consts.h;hb=021197483e8fa9999644c96d7dd7b5c3cc95867a#l59), [illumos](https://github.com/illumos/illumos-gate/blob/052042909d20316395a71c237105dffb9ce6c22f/usr/src/uts/common/sys/siginfo.h#L134), and [musl](https://git.musl-libc.org/cgit/musl/tree/include/signal.h#n37).

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2019-10-05 10:15:20 UTC  
> Url: https://github.com/boostorg/test/pull/231#issuecomment-538636893  

Thanks for the report, this is currently building in branch `topic/PR-231-defines-missing-openbsd`

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2019-10-05 13:11:58 UTC  
> Url: https://github.com/boostorg/test/pull/231#issuecomment-538648608  

Merged to `next`.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2019-10-21 18:28:34 UTC  
> Url: https://github.com/boostorg/test/pull/231#issuecomment-544645844  

In master, closing

---
