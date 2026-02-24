# #464 - Please add OS for GNU/Hurd and GNU/kFreeBSD [Open]

> Username: mirabilos  
> Created at: 2019-08-06 18:00:51 UTC  
> Updated at: 2021-06-11 01:55:29 UTC  
> Url: https://github.com/boostorg/build/issues/464  

While [on adding platforms anyway](https://github.com/boostorg/build/issues/388), please add `BOOST_OS_HURD`.  
  
On GNU/Hurd, the C præprocessor define `__GNU__` is defined, for detection.  
  
See the thread around https://lists.debian.org/debian-hurd/2019/07/msg00045.html where the Hurd porters told me that Boost currently does not have an OS defined for them.  
  
Thanks in advance!

---

## Comment 1

> Username: mirabilos  
> Created at: 2019-08-15 17:11:15 UTC  
> Url: https://github.com/boostorg/build/issues/464#issuecomment-521720227  

Judging from [the latest kfreebsd-amd64 build log](https://buildd.debian.org/status/fetch.php?pkg=performous&arch=kfreebsd-amd64&ver=1.1%2Bgit20190701.9928c27-2&stamp=1564507585&raw=0), there’s also no OS definition for GNU/kFreeBSD (the FreeBSD kernel with GNU userland).  
  
The build fails because neither `BOOST_OS_BSD` nor `BOOST_OS_LINUX` are defined… which is, incidentally, proper, because one needs to use some BSD and some GNU methods on this hybrid operating system; `BOOST_OS_KFREEBSD` would work in addition to `BOOST_OS_HURD`.  
  
Or we can just use…  
  
    #elif defined(__FreeBSD_kernel__) && defined(__GLIBC__)  
  
… to check for it (an _and_ of both is needed to distinguish from regular FreeBSD, and from any system using GNU libc), but then I wonder what these abstractions are for.  
  
(In this specific case, I’d probably use the BSD variant of the code, as it calls a kernel function I assume would still work, but with `linprocfs` mounted, the Linux one would also work. But, as I said, this cannot be generalised.)

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:53 UTC  
> Url: https://github.com/boostorg/build/issues/464#issuecomment-859203269  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
