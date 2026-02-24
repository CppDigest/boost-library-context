# #7 Use std filesystem instead of Boost.Filesystem if available. [Open]

> Username: Mike-Devel  
> Created at: 2018-10-16 10:17:25 UTC  
> Updated at: 2019-03-04 08:42:23 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7  

Makes the cmake based boostdep build independent of an installed boost library when you have a recent c++17 toolchain.  
  
Afaik this currently only works with MSVC2017, as gcc-8 needs special linker flags for now and I don't think it is worth supporting that. I tested it locally, but unfortunately I'm not sure how to hook this up with appveyor CI.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-28 14:55:50 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-433712855  

What special flags are needed on g++ 8?

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-10-28 17:45:30 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-433726028  

Afaik you have to explicitly link the filesystem library with '-lstdc++fs'.

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-10-28 17:51:41 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-433726524  

If that's true, these changes will break `boostdep` on g++ 8.

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2018-10-28 18:14:29 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-433728306  

You mean because the cmake file will link to boost, but main.cpp will compile against std::filesystem?   
I think you are right. Sorry, didn't think this through. I'm not at a dev machine right now,  but I can provide a fix tomorrow or the day after.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-28 18:21:47 UTC  
> Updated_at: 2018-10-28 18:22:31 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-433728896  

Same when using `b2` - the source will try to use `<filesystem>` and fail to link.  
  
Is this worth the trouble? :-)

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2018-10-28 19:17:07 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-433733303  

The fix seems straight forward: Don't detect std::filesystem in source, but ifdef on a flag set by the build system.   
  
I'd consider it worth the trouble to propose the PR, because other than b2, cmake can't trigger the build of boost filesystem on demand (yet). Whether it is worth the trouble for you to  review and maintain it I don't know.

---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2018-10-30 18:34:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostdep/pull/7#pullrequestreview-169935179  

📁 CMakeLists.txt

```diff
  53 |+ 
  54 |+     message(STATUS "Using Boost::filesystem as filesystem library")
  55 |+     if( NOT TARGET Boost::filesystem )
```

> Username: pdimov  
> Created_at: 2018-10-30 18:34:05 UTC  
> Updated_at: 2018-11-05 12:06:08 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#discussion_r229434437  

I'm not fond of `if TARGET` because it's order-dependent.

> Username: Mike-Devel  
> Created_at: 2018-10-30 20:09:59 UTC  
> Updated_at: 2018-11-05 12:06:08 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#discussion_r229467035  

True. Shall I remove it?


---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2018-11-02 12:04:27 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-435357376  

So, I removed the check for the boost::filesystem target and added a test to travis to make sure my changes don't break gcc 8. Do you think it is worth it to integrate into the main repo? If so, any other changes you want me to make?

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-11-02 16:16:30 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-435432992  

The fact that `dist/bin/boostdep --list-exceptions` apparently doesn't output anything (https://travis-ci.org/boostorg/boostdep/jobs/449797943) worries me a little, but I don't have time to investigate.  
  
Apart from that, were I to integrate this, I'd probably drop all autodetections and just let the user choose `-DBOOSTDEP_USE_STD_FS`.

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2018-11-02 17:44:39 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-435457064  

> The fact that dist/bin/boostdep --list-exceptions apparently doesn't output anything (https://travis-ci.org/boostorg/boostdep/jobs/449797943) worries me a little, but I don't have time to investigate.  
  
Last time I checked, develop and master had the same problem, so I ignored it. It seems to be fixed on develop - will investigate why it still fails on this PR.

---

## Comment 11

> Username: pdimov  
> Created_at: 2018-11-02 18:58:57 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-435476794  

No, they don't have this problem. The job fails because the exceptions differ, but it doesn't fail - like here - because the output is completely missing.

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2018-11-05 12:15:44 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-435854025  

OK, so I found the error: I accidentially inverted an `if <is_folder>` condition.  
I also removed the fs autodetection from cmake and simplified the file as much as possible.   
  
The main remaining problem with this PR - as far as I can tell - is that no CI check actually covers the `std::filesystem` path, as I'm not sure how to best hook this up to appveyor. Do you have a suggestion? Or can you add that check yourself?

---

## Comment 13

> Username: Mike-Devel  
> Created_at: 2018-12-08 11:12:14 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-445451361  

Ping.   
Is there any interest in this or should I just close it?

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-12-08 14:00:09 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-445461400  

Keep it open.  
  
Hasn't the CMake community figured out how to link to stdc++fs or c++fs automatically as required?

---

## Comment 15

> Username: Mike-Devel  
> Created_at: 2018-12-08 18:08:02 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-445478247  

Not that I know of. Here is some information on the topic: https://gitlab.kitware.com/cmake/cmake/issues/17834.  
  
Seems it is less a question of how to do it, but more if they should/want to do it.

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-12-08 18:42:33 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-445480709  

> std::filesystem isn't present in the 7.x branch, and I hope it will be part of libstdc++.so in the 9.x branch, so it should only be needed for 8.x releases.  
  
Sounds like the GCC problem will be going away, but libc++ meanwhile decided to add libc++fs as well.

---

## Comment 17

> Username: pdimov  
> Created_at: 2018-12-08 18:44:43 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-445480853  

Or maybe not. https://wandbox.org/permlink/tlXI44j9XzL7XUqr

---

## Comment 18

> Username: Mike-Devel  
> Created_at: 2018-12-10 16:41:59 UTC  
> Updated_at: 2018-12-10 16:44:35 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-445883446  

Considering that gcc still requires `-latomic` in some scenarios, I won't hold my breath for them to "fix" the `-lstdc++fs` requirement.

---

## Comment 19

> Username: Mike-Devel  
> Created_at: 2019-03-04 08:42:23 UTC  
> Url: https://github.com/boostorg/boostdep/pull/7#issuecomment-469165633  

std::filesystem seems to work with latest trunk versions of clang and gcc (according to the wandbox link you posted). So I guess there is hope.

---
