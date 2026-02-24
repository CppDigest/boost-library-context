# #427 - B2 compilation failure on Cygwin [Closed]

> Username: Lastique  
> Created at: 2019-04-14 18:06:43 UTC  
> Updated at: 2019-05-01 22:17:19 UTC  
> Closed at: 2019-05-01 22:17:19 UTC  
> Url: https://github.com/boostorg/build/issues/427  

Running tools/build/src/engine/build.sh on Cygwin and Cygwin64 fails with the attached log.  
  
[build.log](https://github.com/boostorg/build/files/3077703/build.log)

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-04-14 18:20:44 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-483034399  

Looks like cygwin doesn't properly simulate Unix contrary to its claims.

---

## Comment 2

> Username: Lastique  
> Created at: 2019-04-14 18:23:11 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-483034880  

Well, I don't think it's 100% compatible or will ever be. But these errors make me think of a missing include somewhere.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-04-14 18:37:17 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-483037249  

For example, `readlink` is present in `sys/unistd.h`, guarded with `#if __BSD_VISIBLE || __POSIX_VISIBLE >= 200112 || __XSI_VISIBLE >= 4`. `sys/unistd.h` is included from `unistd.h` and the macro checks require `_BSD_SOURCE`, `_POSIX_C_SOURCE >= 200112`, `_XOPEN_SOURCE >= 500`, `defined(_XOPEN_SOURCE) && defined(_XOPEN_SOURCE_EXTENDED)` or `_GNU_SOURCE`. I think, it's one of these macros you may be missing.

---

## Comment 4

> Username: Lastique  
> Created at: 2019-04-14 18:52:11 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-483039723  

In terms of POSIX, it seems you're using mutually exclusive sets of system calls. I tried compiling with `_XOPEN_SOURCE=700`, but, for example, `alphasort` is conditioned on `__POSIX_VISIBLE >= 200809` and `vfork` is `__XSI_VISIBLE >= 4 && __POSIX_VISIBLE < 200809`.  
  
Compiling with `_GNU_SOURCE` or `_BSD_SOURCE` succeeds.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-04-17 18:23:38 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-484206372  

Is `-std=c++11` being used? Cygwin requires `-std=gnu++11` if I'm not mistaken.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-04-17 18:26:01 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-484207230  

That is, POSIX functions are not enabled under Cygwin unless -std=gnu++11 is used. (Or nothing, as the default for 7.4 is -std=gnu++14.)

---

## Comment 7

> Username: Lastique  
> Created at: 2019-04-17 18:34:21 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-484210251  

It uses [-std=c++11](https://github.com/boostorg/build/blob/develop/src/engine/build.sh#L173). But I haven't noticed any checks for C++ version or GNU extensions in the preprocessor checks.  
  
I know Solaris headers are broken a similar way, though. They require different versions of `_XOPEN_SOURCE` depending on whether you're building in C++03 or C++11 mode.

---

## Comment 8

> Username: pdimov  
> Created at: 2019-04-17 19:07:50 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-484221865  

`__STRICT_ANSI__` is checked a number of times in `sys/features.h`.

---

## Comment 9

> Username: grafikrobot  
> Created at: 2019-05-01 19:44:26 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-488393065  

Is the conclusion on this that I should be using `-std=gnu11` on Cygwin?

---

## Comment 10

> Username: pdimov  
> Created at: 2019-05-01 21:04:02 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-488426069  

Either that, or nothing; Cygwin has gcc 7 at the moment, which defaults to -std=gnu++14.

---

## Comment 11

> Username: grafikrobot  
> Created at: 2019-05-01 21:26:12 UTC  
> Url: https://github.com/boostorg/build/issues/427#issuecomment-488436582  

I'd rather be explicit to reduce the risk of someone adding a post 11 dependency.
