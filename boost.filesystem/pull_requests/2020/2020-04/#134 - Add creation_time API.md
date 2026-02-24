# #134 Add creation_time API [Closed]

> Username: soroshsabz  
> Created at: 2020-04-11 21:57:47 UTC  
> Updated at: 2020-08-19 18:02:34 UTC  
> Closed at: 2020-08-19 18:02:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134  

ITNOA  
  
This PR resolved #133   
  
I added support for below platform  
  
- [x] windows  
- [x] linux with glibc greater than 2.28  
- [x]  Linux with glibc less than 2.28  
- [x] FreeBSD  
- [x] Mac OSX greater than Mac OS X 10.5 (Leopard)  
  
I add another platform in coming days.  
  
I would be happy to review my PR.  
  
thanks a lot

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2020-04-12 11:00:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/134#pullrequestreview-391845376  

📁 test/operations_test.cpp

```diff
1196 |                                                       //  but unlikely to be OK on POSIX
1197 |-     BOOST_TEST(ec);
1197 |+     BOOST_TEST(!ec);
```

> Username: Lastique  
> Created_at: 2020-04-12 11:00:59 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r407181823  

Why this change?

> Username: soroshsabz  
> Created_at: 2020-04-12 21:14:56 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r407253089  

I revert it.


---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2020-04-12 11:01:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/134#pullrequestreview-391845439  

📁 src/operations.cpp

```diff
1406 |+ 
1407 |+   struct statx path_stat;
1408 |+   if (error(::statx(AT_FDCWD, p.c_str(), AT_SYMLINK_NOFOLLOW | AT_NO_AUTOMOUNT |
```

> Username: Lastique  
> Created_at: 2020-04-12 11:01:58 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r407181918  

The presence of `statx` needs to be tested at compile time.

> Username: soroshsabz  
> Created_at: 2020-04-12 21:32:54 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r407254742  

I check it right now :)


---

## Comment 3

> Username: soroshsabz  
> Created_at: 2020-04-14 21:18:41 UTC  
> Updated_at: 2020-04-14 21:20:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#issuecomment-613686707  

Finally I can make green Linux tests in travis :) the only one build remaining in travis, it is Mac OSX

---

## Comment 4

> Username: soroshsabz  
> Created_at: 2020-04-16 03:57:45 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#issuecomment-614401927  

@Lastique Thanks for review my code I try to resolve all comments and support all desired platforms. so I happy to review it again.  
  
Thanks

---

## Comment 5

> Username: soroshsabz  
> Created_at: 2020-04-30 18:32:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#issuecomment-622026639  

@Lastique I need this feature, so I thankful for review it :)

---

## Review 6 [Changes requested]

> Username: Lastique  
> Created_at: 2020-05-01 12:47:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/filesystem/pull/134#pullrequestreview-404127499  

📁 src/operations.cpp

```diff
1456 |+ # else
1457 |+   // TODO: Check for symbolic file
1458 |+   error(BOOST_ERRNO, p, ec, "boost::filesystem::creation_time does not support on this platform");
```

> Username: Lastique  
> Created_at: 2020-05-01 11:56:39 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418513548  

`BOOST_ERRNO` has no relevant information here. Use `ENOSYS` or a similar error code.

> Username: soroshsabz  
> Created_at: 2020-07-31 16:56:27 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r463722882  

I correct it

---

📁 src/operations.cpp

```diff
 131 |+ #   ifdef __linux__
 132 |+ #     ifdef __GLIBC__
 133 |+ #       include <gnu/libc-version.h>
```

> Username: Lastique  
> Created_at: 2020-05-01 12:04:00 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418515276  

No internal header includes, please. glibc version macros are not defined in this header anyway. Use `unistd.h` or another standard header to get them.

---

📁 src/operations.cpp

```diff
 132 |+ #     ifdef __GLIBC__
 133 |+ #       include <gnu/libc-version.h>
 134 |+ #       if __GLIBC_MINOR__ > 27
```

> Username: Lastique  
> Created_at: 2020-05-01 12:04:20 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418515355  

Use `__GLIBC_PREREQ` to test glibc version.

---

📁 src/operations.cpp

```diff
 137 |+ #     endif
 138 |+ #     ifndef BOOST_HAS_NATIVE_STATX
 139 |+ #       include "linux_statx.hpp"
```

> Username: Lastique  
> Created_at: 2020-05-01 12:11:05 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418516867  

Remove this, as per the comment for this header.

---

📁 src/operations.cpp

```diff
  99 |+ // The stat64() system calls first appeared in Mac OS X 10.5 (Leopard)
 100 |+ #     if (defined(__MAC_OS_X_VERSION_MIN_REQUIRED) && __MAC_OS_X_VERSION_MIN_REQUIRED >= 1060)
 101 |+ #       define _DARWIN_USE_64_BIT_INODE
```

> Username: Lastique  
> Created_at: 2020-05-01 12:19:57 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418519107  

This macro must be defined before any headers are included. Any header may potentially include `sys/stat.h`, directly or indirectly.

---

📁 src/operations.cpp

```diff
1434 |+   // The stat64() system calls first appeared in Mac OS X 10.5 (Leopard)
1435 |+ #   if (defined(__MAC_OS_X_VERSION_MIN_REQUIRED) && __MAC_OS_X_VERSION_MIN_REQUIRED >= 1060)
1436 |+ #     ifdef _DARWIN_USE_64_BIT_INODE
```

> Username: Lastique  
> Created_at: 2020-05-01 12:22:11 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418519692  

This is incorrect, as this is the macro you define to request the 64-bit inodes. Whether the feature is enabled is indicated by `_DARWIN_FEATURE_64_BIT_INODE`. http://polarhome.com/service/man/?qf=stat&tf=2&of=Darwin&sf=2

---

📁 src/operations.cpp

```diff
1433 |+ # elif defined(__APPLE__)
1434 |+   // The stat64() system calls first appeared in Mac OS X 10.5 (Leopard)
1435 |+ #   if (defined(__MAC_OS_X_VERSION_MIN_REQUIRED) && __MAC_OS_X_VERSION_MIN_REQUIRED >= 1060)
```

> Username: Lastique  
> Created_at: 2020-05-01 12:24:42 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418520376  

I may be missing something, but it looks like when this condition is not satisfied, the function is left empty.  
  
If `st_birthtimespec` is not available on older versions of Mac OS X then the function must return `ENOSYS`.

---

📁 src/operations.cpp

```diff
1453 |+ 	"boost::filesystem::creation_time"))
1454 |+       return std::time_t(-1);
1455 |+   return std::time_t(path_stat.st_birthtim.tv_sec);
```

> Username: Lastique  
> Created_at: 2020-05-01 12:41:19 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418525078  

I think, `st_birthtime` is a more portable way to access this field instead of `st_birthtim.tv_sec`. Also, NetBSD seems to support it as well: https://netbsd.gw.com/cgi-bin/man-cgi?stat+2.i386+NetBSD-8.0

> Username: soroshsabz  
> Created_at: 2020-08-04 20:13:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r465303288  

it is only work, when _POSIX_SOURCE not defined, so I think it is dangerous to use this.  
  
Please see https://www.freebsd.org/cgi/man.cgi?query=stat&sektion=2


📁 src/linux_statx.hpp

```diff
  16 |+ 
  17 |+ // Only for internal use
  18 |+ // Copy many contents from stat.h
```

> Username: Lastique  
> Created_at: 2020-05-01 12:00:35 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418514491  

I don't like this. Please, don't reimplement libc. Aside from maintenance cost, it is also unclear from the licensing perspective (you cannot copy/paste third party code and relicense it under BSL). If libc does not implement the necessary API then make Boost.Filesystem return `ENOSYS`.

> Username: soroshsabz  
> Created_at: 2020-07-31 19:20:15 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r463788103  

@Lastique  I think it is very useful to implement internal implementation of statx syscall when glibc does not supported and it is cause to our library is more useful, but as you wish I remove it.


📁 test/operations_test.cpp

```diff
 260 |+   void bad_creation_time()
 261 |+   {
 262 |+     fs::creation_time(".");
```

> Username: Lastique  
> Created_at: 2020-05-01 12:10:05 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418516665  

Why is this guaranteed to throw? Is this a portable assumption? I could imagine, it should return the creation time of the current directory.  
  
If you want to simulate error, try passing a path to a non-existing file, for example.

---

📁 test/operations_test.cpp

```diff
  51 | 
  52 |+ #if defined(BOOST_POSIX_API) && defined(__linux__)
  53 |+ # include <linux/version.h>
```

> Username: Lastique  
> Created_at: 2020-05-01 12:44:39 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418526054  

Is this necessary? Could you run the tests unconditionally and simply skip them if you get `ENOSYS`?

---

📁 test/operations_test.cpp

```diff
1781 |+     std::time_t ft = fs::last_write_time(f1x);
1782 |+     BOOST_TEST(ft == fs::creation_time(f1x));
1783 |+ # endif
```

> Username: Lastique  
> Created_at: 2020-05-01 12:47:32 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r418526916  

This doesn't run the test on POSIX systems. Just run the tests unconditionally and return if you get `ENOSYS`.

> Username: soroshsabz  
> Created_at: 2020-07-31 19:38:50 UTC  
> Updated_at: 2020-08-04 20:06:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#discussion_r463796807  

I fix it.


---

## Comment 7

> Username: soroshsabz  
> Created_at: 2020-08-04 20:14:36 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#issuecomment-668802755  

@Lastique I am sorry for my long delay, but I think I resolves many of your comment, and you can review it again.  
  
thanks a lot

---

## Comment 8

> Username: Lastique  
> Created_at: 2020-08-19 18:02:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/134#issuecomment-676576376  

Thanks for your work. I have looked through your code and there were issues with it. For example, you are not checking the `stx_mask` field to see if the requested data was actually retrieved. Also, `AT_SYMLINK_NOFOLLOW` is not correct, as the function has to resolve symlinks, like `last_write_time` does. There were other issues as well, so I decided to implement this myself, taking some bits of your PR as an example. My current version is [here](https://github.com/boostorg/filesystem/commit/1eec6f443d1fb1c17a05822caa10eba87635f897).

---
