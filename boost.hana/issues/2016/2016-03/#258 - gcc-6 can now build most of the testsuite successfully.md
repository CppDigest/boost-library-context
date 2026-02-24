# #258 - gcc-6 can now build most of the testsuite successfully [Closed]

> Username: octoploid  
> Created at: 2016-03-04 10:03:53 UTC  
> Updated at: 2016-03-08 12:06:31 UTC  
> Closed at: 2016-03-07 21:53:15 UTC  
> Url: https://github.com/boostorg/hana/issues/258  

gcc-6 can now build most of the Hana testsuite successfully, see:  
http://www.boost.org/development/tests/develop/developer/hana.html  
 (trippels-powerpc64le-gcc-6.0 column)  
  
Maybe it is time to remove the gcc warning:  
# warning "You appear to be using GCC, which is not supported by Hana yet."?

---

## Comment 1

> Username: ldionne  
> Created at: 2016-03-04 21:19:23 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192469709  

I'm still getting around 151 failures with GCC trunk locally, so IMO it's still a bit early to remove the `#warning`. I do agree that many C++14 bugs have been fixed in GCC recently, so it would be worth re-investigating what can be done to support the compiler. That probably means submitting new bug reports, but they're almost there.

---

## Comment 2

> Username: octoploid  
> Created at: 2016-03-05 07:46:08 UTC  
> Updated at: 2016-03-05 08:37:03 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192602573  

Thanks.  
I think https://gcc.gnu.org/bugzilla/show_bug.cgi?id=47226 is the only one left.  
Maybe you could work around it?  
And any new gcc bug report would of course be welcome.  
  
There is also: https://llvm.org/bugs/show_bug.cgi?id=23844  
where Clang accepts an invalid declaration of a member template in a local class.  
From test/type/is_valid.cpp:  
`97         struct yes { template <typename ...> using nested = void; };`

---

## Comment 3

> Username: octoploid  
> Created at: 2016-03-05 11:03:00 UTC  
> Updated at: 2016-03-05 11:04:45 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192622899  

```  
example % for f in **/*.cpp ; do echo $f && g++ -O2 -I/var/tmp/hana/include $f && ./a.out ; done  
  
shows:  
  
misc/tree.cpp  
Assertion failed: (hana::sum<>(tree) == 10), function main, file misc/tree.cpp, line 134.  
scan_right.cpp  
Assertion failed: (hana::scan_right(hana::make_tuple(1, "2", '3'), 4, f) == hana::make_tuple( "f(1, f(2, f(3, 4)))", "f(2, f(3, 4))", "f(3, 4)", 4 )), function main, file scan_right.cpp, line 31.  
```

---

## Comment 4

> Username: ldionne  
> Created at: 2016-03-05 18:58:59 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192706359  

With the latest changes I pushed on develop, we should be down to one runtime failure on GCC trunk (in `test/functional/apply.cpp`, caused by [bug 70096](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=70096)). Do you know how we can setup Travis CI support for GCC trunk? That would be the next step before I can officially say that GCC is supported.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-03-05 19:05:05 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192707846  

Also, I can't reproduce the error in `scan_right.cpp`... what platform are you on?

---

## Comment 6

> Username: octoploid  
> Created at: 2016-03-05 19:20:27 UTC  
> Updated at: 2016-03-05 19:25:40 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192712371  

I'm on x86_64 gnu-Linux.  
  
scan_right.cpp only fails if optimizations are enabled, so it might be a gcc wrong code bug.  
  
```  
markus@x4 example % g++ -g -I../include scan_right.cpp  
markus@x4 example % ./a.out  
markus@x4 example % g++ -O2 -g -I../include scan_right.cpp  
markus@x4 example % ./a.out  
Assertion failed: (hana::scan_right(hana::make_tuple(1, "2", '3'), 4, f) == hana::make_tuple( "f(1, f(2, f(3, 4)))", "f(2, f(3, 4))", "f(3, 4)", 4 )), function main, file scan_right.cpp, line 31.  
[1]    27943 abort      ./a.out  
```  
  
(I have no idea about gcc-6 Travis CI support)

---

## Comment 7

> Username: octoploid  
> Created at: 2016-03-05 19:29:02 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192713109  

No. You're invoking undefined behavior, see http://gcc.gnu.org/gcc-6/porting_to.html  
(More aggressive optimization of -flifetime-dse).  
  
-flifetime-dse=1 "fixes" the issue.

---

## Comment 8

> Username: ldionne  
> Created at: 2016-03-05 19:33:25 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192713375  

Thanks, I'm on it. I only ran the tests with GCC in Debug mode.

---

## Comment 9

> Username: badair  
> Created at: 2016-03-05 20:55:10 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192733953  

> Do you know how we can setup Travis CI support for GCC trunk?  
  
@ldionne I looked into this about a week ago. I don't know how to do this without building from source, which I assume would be highly frowned upon. As far as I can tell, GCC 5.2.1 is the latest package available. I don't know if the same can be said for Appveyor.

---

## Comment 10

> Username: ldionne  
> Created at: 2016-03-05 21:44:28 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192748001  

@badair Thanks for the info. Building from source will be way too long. That sucks.

---

## Comment 11

> Username: ldionne  
> Created at: 2016-03-05 21:45:13 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192748143  

@octoploid I fixed the `scan_right` bug locally; I'm testing and I'll push the fix. I'll also run all the tests in release mode with GCC. We're almost there!!!

---

## Comment 12

> Username: octoploid  
> Created at: 2016-03-06 09:09:19 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192842562  

Thanks. All looks good now.   
commit dd92656da819 can now be reverted, because the gcc bug was fixed.  
  
Compile times look good for gcc:  
1500.02s user 78.09s system 380% cpu 6:55.02 total  
  
clang is much slower:  
 2122.16s user 58.94s system 285% cpu 12:42.68 total  
  
(make -j4 check with CXXFLAGS="-O2". Both compilers were build with checking=release and asserts enabled.)

---

## Comment 13

> Username: ldionne  
> Created at: 2016-03-06 14:18:32 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192895196  

Two things:   
1. I'm still getting a segfault in `test.functional.apply` because of [70096](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=70096). This appears to be the last problem remaining.  
2. How do you build GCC with optimizations enabled? My GCC is a total slowpoke, taking over an hour to complete the test suite.

---

## Comment 14

> Username: octoploid  
> Created at: 2016-03-06 14:26:06 UTC  
> Updated at: 2016-03-06 14:36:55 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-192895819  

@ldionne   
1. I build with CXXFLAGS="-O2", so I don't hit the issue.  
2. Just configure gcc with --enable-checking=release  
  
BTW github_234.cpp fails on a ppc64le POWER8 machine:  
  
```  
/home/trippels/hana/include/boost/hana/equal.hpp:60:13: error: static assertion failed: No default implementation of hana::equal is provided for related types that can't be safely embedded into a common type, because those are most likely programming errors. If this is really what you want, you can manually convert both objects to a common Comparable type before performing the comparison.  
             static_assert(!hana::is_convertible<T_, U>::value &&  
             ^~~~~~~~~~~~~  
```  
  
(build times on ppc64le (160 "core" machine) with -O2:  
  
gcc:     make -j60 check  2987.82s user 234.45s system 2737% cpu 1:57.72 total  
clang:  make -j60 check  4060.59s user 158.18s system 2494% cpu 2:49.12 total)

---

## Comment 15

> Username: ldionne  
> Created at: 2016-03-06 23:09:29 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-193013886  

@octoploid  Is it possible that `char` is unsigned on that machine?

---

## Comment 16

> Username: octoploid  
> Created at: 2016-03-07 06:14:29 UTC  
> Updated at: 2016-03-07 14:52:20 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-193119371  

Yes and ppc64 is not the only arch:  
  
```  
Signed                  Unsigned  
alpha                   aarch64  
i686                    arm  
ia64                    ppc  
m68k                    ppc64  
mips                    s390  
sparc                   s390x  
x86_64  
```  
  
You can use -fsigned-char as a workaround. It fixes the issue for me.

---

## Comment 17

> Username: gnzlbg  
> Created at: 2016-03-07 14:48:59 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-193279150  

There is an open issue for whitelisting GCC 6 on travis (it seems that there are packages for it in ubuntu-toolchain-r-test):   
  
Issue: https://github.com/travis-ci/apt-package-whitelist/issues/2294  
  
Packages: https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test

---

## Comment 18

> Username: ldionne  
> Created at: 2016-03-07 21:49:31 UTC  
> Updated at: 2016-03-07 21:53:51 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-193469260  

@octoploid Thanks, fixed.  
@gnzlbg Looks like we'll have to wait for the Travis team.. Thanks for the cross-reference. **Edit:** Travis support for GCC 6 will be added by #259.

---

## Comment 19

> Username: ldionne  
> Created at: 2016-03-07 21:53:15 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-193471525  

63c5f87dfdadd07e496cefefc63c3ea9968d3140 removes the warning about GCC and documents support for GCC. GCC 6 officially supports Hana!

---

## Comment 20

> Username: rhalbersma  
> Created at: 2016-03-08 12:06:31 UTC  
> Url: https://github.com/boostorg/hana/issues/258#issuecomment-193756373  

@gnzlbg AFAICS, gcc-6 is only available for the upcoming Ubuntu 16.04 LTS, not yet for 12.04 / 14.04.  Or did you just forcibly install the 16.04 package on an older distro?
