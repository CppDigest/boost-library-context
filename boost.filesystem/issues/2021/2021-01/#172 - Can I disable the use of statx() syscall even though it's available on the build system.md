# #172 - Can I disable the use of statx() syscall even though it's available on the build system? [Closed]

> Username: Gei0r  
> Created at: 2021-01-11 11:52:56 UTC  
> Updated at: 2022-01-06 14:53:03 UTC  
> Closed at: 2021-01-11 12:05:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172  

During build, the test `has_statx_syscall` in [Jamfile.v2](https://github.com/boostorg/filesystem/blob/develop/config/Jamfile.v2) is perfomed to check if `statx()` is available. Because it is available on my build system, boost::filesystem uses the `statx` syscall.  
However, my executable also has to run on older kernels which don't have this syscall yet (before 4.11).  
  
How can I disable the use of the statx syscall, preferably in the `b2` invocation while building boost::filesystem?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-01-11 12:05:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-757909813  

There is no switch to disable `statx`, or any other modern system APIs used in Boost.Filesystem. This is deliberate. The recommendation is to build Boost on the system that is equivalent to that which will run the executable. That means both libc and kernel headers must match (or be older than) the kernel that will run the executable. Another solution is to patch your version of Boost, of course.

---

## Comment 2

> Username: luke-jr  
> Created at: 2021-03-30 01:34:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-809838412  

> The recommendation is to build Boost on the system that is equivalent to that which will run the executable. That means both libc and kernel headers must match (or be older than) the kernel that will run the executable.   
  
But it *doesn't* mean that. It's pretty common to have kernel headers newer than the actual kernel, even with only one system involved (ie, build system itself now can't run it!). Boost is the first project I've heard of having a problem with this.

---

## Comment 3

> Username: luke-jr  
> Created at: 2021-03-30 18:35:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-810486622  

Furthermore, it isn't actually possible to use old linux-headers anymore. glibc build fails with:  
  
```  
configure: error: GNU libc requires kernel header files from  
Linux 3.2.0 or later to be installed before configuring.  
```

---

## Comment 4

> Username: Lastique  
> Created at: 2021-03-30 18:58:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-810500792  

If you're using an old kernel then you might have to use older userspace, including libc and Boost.

---

## Comment 5

> Username: luke-jr  
> Created at: 2021-03-30 19:13:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-810512156  

That's unreasonable.

---

## Comment 6

> Username: Lastique  
> Created at: 2021-03-30 20:15:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-810548161  

Not at all. Newer software is expected to require newer third party components. Not that Boost.Filesystem requires - it doesn't, as long as you use proper headers. From my POV, supporting inconsistent configurations is what's unreasonable. The fact that this is to support Linux kernel 2.6.32, which was [released 9 years ago](https://lkml.org/lkml/2012/3/4/58) or more, depending on the patch version, is not a compelling argument either.  
  
Boost.Filesystem uses a number of modern Linux syscalls, and that number will probably grow in the future. Introducing an option for each of them is unmaintainable in the long term. And it will not resolve the issue in general anyway, because some functionality may not involve a separate syscall, but a macro or a parameter value for example. So no, mismatched configs are not supported, period.

---

## Comment 7

> Username: luke-jr  
> Created at: 2021-03-30 21:31:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-810591060  

>From my POV, supporting inconsistent configurations is what's unreasonable.   
  
It is a normal configuration to have a newer headers than kernel.  
  
>Boost.Filesystem uses a number of modern Linux syscalls, and that number will probably grow in the future. Introducing an option for each of them is unmaintainable in the long term.   
  
I agree. Generally, well-designed software can simply fall back to other (often standards-defined) methods when the optimal code path is unsupported.  
  
For example (just one instance, optimised for smallest diff size):  
  
```diff  
diff --git a/operations.cpp b/operations.cpp  
index fc853fb..0397fe6 100644  
--- a/operations.cpp  
+++ b/operations.cpp  
@@ -1202,25 +1202,33 @@ bool copy_file(const path& from, const path& to, unsigned int options, error_cod  
     break;  
   }  
   
+  mode_t from_mode;  
 #if defined(BOOST_FILESYSTEM_HAS_STATX) || defined(BOOST_FILESYSTEM_HAS_STATX_SYSCALL)  
   unsigned int statx_data_mask = STATX_TYPE | STATX_MODE | STATX_INO | STATX_SIZE;  
   if ((options & static_cast< unsigned int >(copy_options::update_existing)) != 0u)  
     statx_data_mask |= STATX_MTIME;  
   
+{  
   struct ::statx from_stat;  
   if (BOOST_UNLIKELY(statx(infile.fd, "", AT_EMPTY_PATH | AT_NO_AUTOMOUNT, statx_data_mask, &from_stat) < 0))  
   {  
-  fail_errno:  
-    err = errno;  
-    goto fail;  
+    if (errno == ENOSYS)  
+    {  
+      goto fallback:  
+    }  
+    goto fail_errno;  
   }  
   
-  if (BOOST_UNLIKELY((from_stat.stx_mask & statx_data_mask) != statx_data_mask))  
+  if (BOOST_LIKELY((from_stat.stx_mask & statx_data_mask) == statx_data_mask))  
   {  
-    err = ENOSYS;  
-    goto fail;  
+    from_mode = get_mode(from_stat);  
+    goto have_mode;  
   }  
-#else  
+}  
+  
+fallback:  
+#endif  
+{  
   struct ::stat from_stat;  
   if (BOOST_UNLIKELY(::fstat(infile.fd, &from_stat) != 0))  
   {  
@@ -1228,9 +1236,11 @@ bool copy_file(const path& from, const path& to, unsigned int options, error_cod  
     err = errno;  
     goto fail;  
   }  
-#endif  
   
-  const mode_t from_mode = get_mode(from_stat);  
+  from_mode = get_mode(from_stat);  
+}  
+  
+have_mode:  
   if (BOOST_UNLIKELY(!S_ISREG(from_mode)))  
   {  
     err = ENOSYS;  
```

---

## Comment 8

> Username: Lastique  
> Created at: 2021-03-30 22:23:58 UTC  
> Updated at: 2021-03-30 22:24:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-810618466  

> It is a normal configuration to have a newer headers than kernel.  
  
I don't consider that normal. That's certainly not the case with the systems I work with.

---

## Comment 9

> Username: Gei0r  
> Created at: 2021-03-31 20:10:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-811428731  

I'm inclined to agree that mismatching headers and used kernel are not common. For example, with crosstool-NG, you can select which kernel headers to install to match the target system kernel.  
  
However, I believe my original use case (build on a newer system than is used for running) is common. With regards to `statx()`, `boost::filesystem` already includes the fallback code, using regular old `stat()`. I believe making the fallback detection in the Jamfile configurable would not impact maintainability.

---

## Comment 10

> Username: luke-jr  
> Created at: 2021-03-31 20:20:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-811435142  

>I'm inclined to agree that mismatching headers and used kernel are not common.  
  
This is the standard behaviour for Gentoo... linux-headers is installed and updated independently from whatever kernel is being used (which users expect to be able to swap-and-go without needing changes to userspace, so building against the latest headers is needed to avoid a rebuild when you switch to a new kernel).  
  
>However, I believe my original use case (build on a newer system than is used for running) is common.  
  
Indeed, that's another real-world issue that we're going to have to address in Bitcoin Core. We have some minimal glue to enable users to run with ancient glibcs (missing newer versions of standard functions), but didn't realise Boost has this problem. :/

---

## Comment 11

> Username: Lastique  
> Created at: 2021-04-01 06:17:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-811673259  

> so building against the latest headers is needed to avoid a rebuild when you switch to a new kernel  
  
This is backwards. You can compile against older headers and run on a newer kernel as the kernel maintains backward compatibility. You can't do the opposite because the kernel does not maintain forward compatibility.  
  
If Gentoo does not provide a way to install headers matching the kernel then their setup is broken and you should report a bug in their issue tracker.

---

## Comment 12

> Username: paresy  
> Created at: 2021-04-01 11:53:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-811855053  

This also fails for any older docker builds: https://github.com/docker/for-linux/issues/208  
  
If you have an outdated docker version (e.g. you use Docker on Synology) upgrading to Boost 1.75 will fail miserably.

---

## Comment 13

> Username: luke-jr  
> Created at: 2021-04-01 14:59:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-811967392  

>This is backwards. You can compile against older headers and run on a newer kernel as the kernel maintains backward compatibility. You can't do the opposite because the kernel does not maintain forward compatibility.  
  
Backward compatibility is a job of the userspace application wishing to use headers directly. Using standard C/C++ puts this job on the stdlib (which it tends to handle fine).  
  
>If Gentoo does not provide a way to install headers matching the kernel then their setup is broken and you should report a bug in their issue tracker.  
  
Point is that the kernel may be different without changing userspaces.

---

## Comment 14

> Username: Lastique  
> Created at: 2021-04-01 16:42:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812032255  

> Backward compatibility is a job of the userspace application wishing to use headers directly.  
  
No. Compatibility (forward or backward) is a result of guarantees provided by the kernel. The kernel only guarantees backward compatibility. Some userspace software jumps through hoops to detect kernel features in runtime despite what was available at compile time, but that is definitely not how software normally works.

---

## Comment 15

> Username: luke-jr  
> Created at: 2021-04-01 16:50:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812037348  

Software normally uses the standard libraries, not interacting with the kernel directly...  
  
It's literally impossible for Linux itself to solve this. It can't know about syscalls that didn't exist when it was released.  
  
So basically you're saying anyone providing precompiled binaries can't use Boost at all... (Building on an ancient system often isn't possible, and even if it was, would miss out on the new features provided by newer kernels)

---

## Comment 16

> Username: Lastique  
> Created at: 2021-04-01 17:01:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812043317  

> So basically you're saying anyone providing precompiled binaries can't use Boost at all...  
  
No, I'm not saying that. But if someone is shipping precompiled binaries for system X, then he should presumably have built those binaries on that system X. Those binaries will then be compatible with system X and later. If that system X is ancient, well that's your choice to support it.

---

## Comment 17

> Username: Lastique  
> Created at: 2021-04-01 17:03:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812044797  

> on that system X  
  
That should say "for that system X", meaning that the compilation should use headers and libraries from that system X. This may be done in chroot environment on a newer system, for example.

---

## Comment 18

> Username: luke-jr  
> Created at: 2021-04-01 17:16:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812051814  

That ancient system X likely won't have a C++17 compiler (much less a recent-enough Boost)...

---

## Comment 19

> Username: paresy  
> Created at: 2021-04-01 18:56:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812106278  

I'd like to reiterate on my comment. Docker 18.09 might seem old, but it is actively used on all Synology systems running the current DSM 6. Therefore, anyone using Boost 1.75 for building Docker images that shall run on Synology systems through Docker will be out of luck.  
  
I can just recommend to use this patch to completely disable this optimization: https://github.com/cms-sw/cmsdist/blob/17b1cc0e73e4a640d81a7fc5a92835505a006a16/boost-1.75.0-disable-statx.patch  
  
In my opinion this should be fixed inside Boost with a fallback to the old mechanism, when statx is not available. I always though about Boost as a solid incubator and polyfill library that maximizes compatibility, if a recent compiler/stdlib is not available on the specific platform. I am not sure why this issue seem to be the exception.

---

## Comment 20

> Username: Lastique  
> Created at: 2021-04-03 07:03:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812824512  

> Docker 18.09 might seem old, but it is actively used on all Synology systems running the current DSM 6.  
  
@paresy I'm not a Docker or Synology user, but the same recommendation to use matching headers apply. If it cannot be implemented in your setup, please explain why.

---

## Comment 21

> Username: paresy  
> Created at: 2021-04-03 09:11:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812838383  

@Lastique The headers are matching. We are building with Ubuntu 18.04. And the Docker Container is also running Ubuntu 18.04. The problem is, that Docker whitelisted the statx call just after Docker 18.09. Therefore statx is not available on any Docker installation running 18.09 and older regardless what is running inside the container.   
  
I am using the mentioned patch and the problem is solved for me - but i don't think your stance on the problem is the right one and it will needlessly give a lot of headaches to anyone using Boost Filesystem.

---

## Comment 22

> Username: Lastique  
> Created at: 2021-04-03 15:50:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812883536  

> The headers are matching. We are building with Ubuntu 18.04. And the Docker Container is also running Ubuntu 18.04. The problem is, that Docker whitelisted the statx call just after Docker 18.09. Therefore statx is not available on any Docker installation running 18.09 and older regardless what is running inside the container.  
  
I see, so this is an overly strict sandboxing then. I don't consider this a problem of Boost.Filesystem per se, but if those Docker versions are still popular (I have no idea if they are), this might be a better reason to add a workaround. I believe, in your case the only solution is to disable the use of newer system calls at compile time.

---

## Comment 23

> Username: paresy  
> Created at: 2021-04-03 16:20:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-812887985  

That's what i am doing now. I am not suggesting that Boost is at fault here - it is just a usability issue, which seems to be relevant when counting the created issues about this topic.  
  
Docker 18.09 is unfortunately in use on all Synology NAS devices. The current DSM 6 version is still shipping it. See the release notes here: https://www.synology.com/de-de/releaseNote/Docker (DSM 7 is due sometime later this year)  
QNAP seems to have just recently (March 2021) upgraded to a newer Docker version:  https://www.qnap.com/en/app_releasenotes/list.php?app_choose=container-station

---

## Comment 24

> Username: Gei0r  
> Created at: 2021-04-09 23:47:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-817030446  

Trying to break the deadlock here -- would be too much to ask for boost to handle a `statx()` return with `ENOSYS` by falling back to regular `stat()` *at runtime* (probably caching the result)?

---

## Comment 25

> Username: Lastique  
> Created at: 2021-04-10 00:20:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-817038612  

I don't like the idea of constantly hitting `ENOSYS`. Caching the result has its own issues, thread safety for example. Compile time options seem like a better way.

---

## Comment 26

> Username: sdarwin  
> Created at: 2021-05-07 12:09:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-834317604  

It seems we have also encountered this problem.  Any time you run docker containers, they depend on the host's kernel.  As luke-jr wrote "It's pretty common to have kernel headers newer than the actual kernel".    Keep in mind: running docker, and containers, is very common, out in the real world.  The general principle is that you don't have to be overly careful about matching the host version, and the container version.    Mix-and-match is typical.  
 if you are making programmatic decisions based on the kernel features, be sure the detection mechanism is foolproof.  Ideally, verify that a kernel syscall is actually available and fallback gracefully.

---

## Comment 27

> Username: Lastique  
> Created at: 2021-05-07 15:55:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-834564925  

As I have said before, I do not consider a newer Docker image running on an older kernel a valid use case. If someone is doing this, he is doing it wrong. I will not support this use case, period. If that means no support for Docker, so be it.  
  
I don't plan to add a runtime detection of the features for the purpose of compatibility with inconsistent runtime configs. However, there are some use cases when some syscalls fail for some filesystems and not the other. Those use cases I would like to support, and part of that may involve runtime detection (though, not wrt. `statx`).

---

## Comment 28

> Username: rcombs  
> Created at: 2021-06-09 07:11:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-857447076  

Checking for `ENOSYS` is very common in these kinds of situations, as is doing runtime checks for newer libc routines (e.g. `getrandom`) using `dlsym`. Even glibc internally checks for ENOSYS and falls back on older and better-supported syscalls for routines like `time()` (to support kernels without the `time64` syscall; this also applies to all other libc functions that use other syscalls taking 64-bit time on 32-bit kernels, such as `sigtimedwait`), `preadv`, `fexecve` (for `SYS_execveat`), `fstatat64`, `getdents64`, and various others.  
  
Performance concerns can be addressed fairly trivially by storing an atomic (if multiple threads race past and hit ENOSYS at the same time, it's harmless).  
  
I don't see a reason to require all builds to choose between supporting older kernels at all, or supporting newer features when available.

---

## Comment 29

> Username: paresy  
> Created at: 2021-08-14 07:27:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-898859044  

Just FYI the issue is fixed in Boost 1.77 for me by using the new BOOST_FILESYSTEM_DISABLE_STATX define.  
  
Usage (Correct me if i'm wrong):  
> ./b2 ... define=BOOST_FILESYSTEM_DISABLE_STATX ...

---

## Comment 30

> Username: Gei0r  
> Created at: 2021-08-15 17:30:18 UTC  
> Updated at: 2021-08-15 17:30:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-899085325  

@paresy As I understand it, that's not even necessary, because if `statX()` is not available, this is handled transparently at runtime.  
  
Btw, thanks @Lastique for implementing the change!

---

## Comment 31

> Username: o01eg  
> Created at: 2021-10-18 17:51:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-946017317  

Runtime check could fail on Android because  
  
```  
signal 31 (SIGSYS), code 1 (SYS_SECCOMP), fault addr --------  
Cause: seccomp prevented call to disallowed arm system call 397  
```

---

## Comment 32

> Username: o01eg  
> Created at: 2021-10-18 18:27:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-946046567  

> Just FYI the issue is fixed in Boost 1.77 for me by using the new BOOST_FILESYSTEM_DISABLE_STATX define.  
  
Is it possible to write it in user-config.jam?

---

## Comment 33

> Username: Lastique  
> Created at: 2021-10-18 18:35:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-946052615  

You can set it in your compiler description, such as:  
  
```  
using clang : : clang-12 : <define>BOOST_FILESYSTEM_DISABLE_STATX ;  
```  
  
See Boost.Build [docs](https://www.boost.org/doc/libs/1_77_0/tools/build/doc/html/index.html#bbv2.overview.configuration).

---

## Comment 34

> Username: o01eg  
> Created at: 2021-10-18 20:13:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-946129017  

@Lastique Thank you.

---

## Comment 35

> Username: hjmallon  
> Created at: 2022-01-06 14:51:52 UTC  
> Updated at: 2022-01-06 14:53:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/172#issuecomment-1006651625  

Nvidia Jetson SDK (Nvidia Jetpack Linux 4.6, based on Ubuntu 18.04) seems to ship kernel 4.9 (`4.9.253-tegra`) and `linux-libc-dev` `4.15.0-166.174`, so it hits this problem all the time. `statx` was introduced in 4.11 (https://github.com/torvalds/linux/commit/a528d35e8bfcc521d7cb70aaf03e1bd296c8493f)  
  
I'm going to update Boost and use `BOOST_FILESYSTEM_DISABLE_STATX` as mentioned above but hopefully this allows someone else to get here from a search.
