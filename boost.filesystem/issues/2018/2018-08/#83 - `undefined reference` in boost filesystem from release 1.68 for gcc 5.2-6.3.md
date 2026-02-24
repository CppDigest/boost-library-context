# #83 - `undefined reference` in boost filesystem from release 1.68 for gcc 5.2-6.3 [Closed]

> Username: andrejnau  
> Created at: 2018-08-18 10:06:26 UTC  
> Updated at: 2018-12-09 07:59:49 UTC  
> Closed at: 2018-12-09 07:59:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83  

After update boost from 1.67 to 1.68 our code can't be linked.  
  
Here simple code: https://wandbox.org/permlink/MKhgrdMO1p1aHEOn  
```/tmp/ccXDdraA.o: In function `boost::system::generic_category()':  
prog.cc:(.text._ZN5boost6system16generic_categoryEv[_ZN5boost6system16generic_categoryEv]+0x5): undefined reference to `boost::system::detail::generic_category_instance'  
collect2: error: ld returned 1 exit status```  
  
P.S.  
Can you test others boost component? I think this is not the only problem in this release.

---

## Comment 1

> Username: jbeich  
> Created at: 2018-08-24 08:26:23 UTC  
> Updated at: 2018-08-24 08:32:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-415690107  

Probably, duplicate of either https://github.com/boostorg/system/issues/24 or https://github.com/boostorg/system/issues/26. See also [ABI discussion](https://lists.boost.org/Archives/boost/2018/08/242770.php).

---

## Comment 2

> Username: wkailey  
> Created at: 2018-09-10 21:08:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-420061542  

I just installed boost 1.68, and I have the same problem. After successfully untarring, building, and installing boost, I am including <boost/regex.hpp> and linking with -lboost_regex, and it comes up with an undefined external [a function named . . . <long bunch of template paramteters> . . .match()] at line 386 of regex/v4/perl_matcher.hpp

---

## Comment 3

> Username: jcbritobr  
> Created at: 2018-12-07 23:50:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-445403099  

This also happens to me with mingw 64 builds on windows and gcc 7.x

---

## Comment 4

> Username: pdimov  
> Created at: 2018-12-08 00:09:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-445408504  

It might be a good idea to check 1.69 RC1 at https://dl.bintray.com/boostorg/release/1.69.0/source/  
  
Boost.System is header-only in 1.69, so the link error in the original post should no longer be an issue.  
  
But in general, the rule is that you have to build Boost with the same C++ standard with which you'll be using it. If you're going to use f.ex. C++14, and you're building with a compiler for which C++14 isn't default (such as gcc 5/clang 5 or earlier) you need to build with `b2 cxxstd=14`.

---

## Comment 5

> Username: Lastique  
> Created at: 2018-12-08 16:29:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-445471305  

I'm tempted to close this because it looks like an ABI mismatch problem. I've added a MinGW-w64 gcc 7.3 CI job just to test this config explicitly.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-12-08 17:45:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-445476789  

And it failed. -)

---

## Comment 7

> Username: Lastique  
> Created at: 2018-12-08 20:50:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-445488898  

For a different reason. :)

---

## Comment 8

> Username: jcbritobr  
> Created at: 2018-12-08 22:32:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-445494875  

>   
>   
> It might be a good idea to check 1.69 RC1 at https://dl.bintray.com/boostorg/release/1.69.0/source/  
>   
> Boost.System is header-only in 1.69, so the link error in the original post should no longer be an issue.  
>   
> But in general, the rule is that you have to build Boost with the same C++ standard with which you'll be using it. If you're going to use f.ex. C++14, and you're building with a compiler for which C++14 isn't default (such as gcc 5/clang 5 or earlier) you need to build with `b2 cxxstd=14`.  
  
I was using gcc 7.3, but will build it again, using b2 cxxstd=14.  
  
Thank you guys.

---

## Comment 9

> Username: Lastique  
> Created at: 2018-12-09 07:59:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/83#issuecomment-445518340  

The tests are [passing](https://ci.appveyor.com/project/Lastique/filesystem/builds/20871761/job/4iqxa56sr2sr4685) now. So either this was an ABI mismatch or it was already fixed in develop.
