# #229 - stat/statx usage on Android 9.0+ [Closed]

> Username: Phil25  
> Created at: 2022-02-15 15:21:11 UTC  
> Updated at: 2022-03-02 11:25:08 UTC  
> Closed at: 2022-02-15 15:50:15 UTC  
> Url: https://github.com/boostorg/filesystem/issues/229  

We're supplying a dynamic library which uses Boost for an Android app. Since the seccomp filter introduction in Android 8.0, we've been getting crashes related to `stat` syscalls:  
```  
syscall 0x00000078c59610d0  
boost::filesystem::details::status(boost::filesystem::path const&, boost::system::error_code*) 0x00000077d0dbac64  
boost::filesystem::is_directory(boost::filesystem::path const&, boost::system::error_code&) 0x00000077d0d0a5e0  
```  
  
This is because `stat` was not available in the [syscall whitelist in Android 8.0](https://android.googlesource.com/platform/bionic.git/+/master/libc/SYSCALLS.TXT). The fix was simply updating from Boost 1.76 to 1.77 where runtime detection for `statx` was added.  
  
However, from Android 9.0, `statx` is also not on the [syscall whitelist](https://android.googlesource.com/platform/bionic/+/android-9.0.0_r33/libc/SYSCALLS.TXT) anymore:  
```  
syscall 0x0000007dd7848340  
boost::filesystem::detail::(anonymous namespace)::statx_syscall(int, char const*, int, unsigned int, statx*) 0x0000007c2d6dc0a0  
boost::filesystem::detail::(anonymous namespace)::invoke_statx(int, char const*, int, unsigned int, statx*) 0x0000007c2d6d938c  
boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*) 0x0000007c2d6d6638  
boost::filesystem::is_directory(boost::filesystem::path const&, boost::system::error_code&) 0x0000007c2d58951c  
```  
  
Interestingly, older versions of Boost (1.72) work for newer Androids (9 and 10), which is a bit similar to what #183 reports. #172 hints at using `BOOST_FILESYSTEM_DISABLE_STATX`, but wouldn't this just make Boost fall back to `stat`?  
  
I've been investigating this for a while now but my experience in Android is lacking, what I'm most curious about is this:  
1. I inspected the source code of 1.72, but don't see any other solutions to `stat`/`statx` syscalls than in the newer versions. How come 1.72 works on all Androids we tested (8, 9 and 10), while 1.76 works on neither (except pre-8) and 1.77 works only on 8.  
2. Does `BOOST_FILESYSTEM_DISABLE_STATX` disable the usage of both `stat` and `statx`? Is there another way to call status on a file though Boost?  
3. Is it possible to support a wide range of Androids with one build (from before Linux 4.11 update, where `statx` was added)? The runtime detection of `statx` solved it for us, but if we can't rely on it anymore we might need a different solution.  
4. Am I thinking about this whole thing correctly?  
  
**Android API level**: minimal 24, targeting 31  
**Android SDK**: 28  
**Android NDK**: r16b (both library and the app)  
**Platforms**: armv7 and armv8  
  
Thank you in advance, I can try to fish out any other information 🙂

---

## Comment 1

> Username: Lastique  
> Created at: 2022-02-15 15:50:15 UTC  
> Updated at: 2022-02-15 16:19:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/229#issuecomment-1040440463  

There is no alternative to `stat` or `statx` on POSIX systems, this system call is mandatory for anything related to filesystem, including Boost.Filesystem. If it doesn't work on your system then I'm afraid there is no workaround on Boost.Filesystem side.  
  
`BOOST_FILESYSTEM_DISABLE_STATX` disables `statx` and forces the library to use `stat` instead. There is no replacement for `stat`.  
  
I have no experience with Android development, so I can't suggest a solution other than to configure or disable the syscall filter, if possible. From the syscall list you linked, it looks like the kernel interface requires the applications to use `openat`/`fstatat` family of functions (i.e. no APIs operating on standalone paths are whitelisted). By itself it doesn't necessarily mean such operations are not supported, as they may be emulated in libc (bionic). If not then this system is incompatible with Boost.Filesystem, which relies on operating on files identified by paths, not file descriptors.  
  
I may try to add support for `*at` family of functions in the future, but I'm not sure when.

---

## Comment 2

> Username: Phil25  
> Created at: 2022-03-02 07:46:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/229#issuecomment-1056484476  

Thank you for the confirmation. From our side there's still no indication why older Boost version works on newer Android systems despite using the same syscalls underneath.  
  
Upon further testing, we've confirmed that the last version working on all tested systems is 1.74, which just precedes `statx` support. This of course doesn't help explain why switching to `statx` works for Android 8 exclusively, but I think we will cut our losses here and simply use the older version on Android.  
  
To close this up I'll leave a summary, in case anyone else encountered this problem:  
- Android 7.0 - no syscall restrictions  
- Android 8.0 - whitelists `statx` but not `stat` (I believe 8.0 is where kernel 4.11 was also introduced, which added `statx`)  
- Android 9.0+ - whitelists neither  
- Boost 1.74 - no support for `statx` yet  
- Boost 1.75 - supports `statx`  
- Boost 1.77 - supports runtime detection of `statx`  
  
| Android | Boost 1.74 | Boost 1.75/1.76 | Boost 1.77 |  
| - | - | - | - |  
| 7.0 | works | works | works |  
| 8.0 | works | crash on some devices | works  |  
| 9.0+ | works | crash on some devices | crash on some devices |

---

## Comment 3

> Username: Lastique  
> Created at: 2022-03-02 08:32:21 UTC  
> Updated at: 2022-03-02 08:42:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/229#issuecomment-1056564891  

Boost 1.74 used `stat` exclusively, so if it works across the board then `stat` must work as well. Have you tried building the latest Boost with `BOOST_FILESYSTEM_DISABLE_STATX`?  
  
You may also want to define `BOOST_FILESYSTEM_DISABLE_SENDFILE` and `BOOST_FILESYSTEM_DISABLE_COPY_FILE_RANGE` to disable the corresponding syscalls.

---

## Comment 4

> Username: Phil25  
> Created at: 2022-03-02 09:09:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/229#issuecomment-1056634154  

We haven't tried it because `stat` being blacklisted was the reason for Android 8.0 crashes. Updating to 1.77 which started calling `statx` instead worked for this system. Even if calling `stat` exclusively would work on Androids 9.0+, it would mean we have to build our project twice as many times to provide the non-statx flavor, which is more of a burden than using an older Boost version on Android.  
  
I am curious for the reason for this, most likely there is something here I can't see. But after weeks of testing various combinations that's all I can conclude.

---

## Comment 5

> Username: Lastique  
> Created at: 2022-03-02 10:36:02 UTC  
> Updated at: 2022-03-02 10:41:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/229#issuecomment-1056771772  

> We haven't tried it because `stat` being blacklisted was the reason for Android 8.0 crashes. Even if calling `stat` exclusively would work on Androids 9.0+, it would mean we have to build our project twice as many times to provide the non-statx flavor, which is more of a burden than using an older Boost version on Android.  
  
Again, `stat` is being used in Boost.Filesystem 1.74, and according to your findings it works. I'm not sure I understand why you would build Boost twice - the single build that uses `stat` would work on any Android.  
  
I think your interpretation of the syscall lists is not quite correct. There are [multiple lists](https://android.googlesource.com/platform/bionic/+/android-9.0.0_r33/libc), and `SYSCALLS.TXT` only lists syscalls for which stubs are generated in bionic (i.e. where the libc function simply forwards to the corresponding syscall). There are also a number of `SECCOMP_WHITELIST*.txt` and `SECCOMP_BLACKLIST*.txt` files, which are presumably used for different kind of processes. For example, you can see `stat64` and `lstat64` whitelisted ([8.0](https://android.googlesource.com/platform/bionic/+/android-8.0.0_r4/libc/SECCOMP_WHITELIST.TXT), [9.0](https://android.googlesource.com/platform/bionic/+/android-9.0.0_r33/libc/SECCOMP_WHITELIST_COMMON.TXT)), and `stat` and `lstat` libc functions presumably are implemented in terms of those.  
  
I don't know how whitelists and blacklists interact, and what is the default when a syscall is missing in all lists. If `statx` doesn't work on Android 9.0, and given that it is not present in any of the lists, it looks like it is blocked by default.

---

## Comment 6

> Username: Lastique  
> Created at: 2022-03-02 11:25:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/229#issuecomment-1056816322  

I have disabled the use of `statx` syscall on Android prior to 11.0. On Android 11, libc also introduces a wrapper function for `statx`, so that will be used.
