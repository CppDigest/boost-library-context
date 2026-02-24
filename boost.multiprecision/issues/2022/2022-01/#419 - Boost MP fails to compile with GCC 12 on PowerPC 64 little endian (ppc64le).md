# #419 - Boost MP fails to compile with GCC 12 on PowerPC 64 little endian (ppc64le) [Closed]

> Username: lrineau  
> Created at: 2022-01-24 16:06:10 UTC  
> Updated at: 2022-02-04 11:19:25 UTC  
> Closed at: 2022-02-04 11:19:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419  

*This bug is a duplicate of CGAL/cgal#6271. You can reply to either of them, and I will coordinate the status of both bugs.*  
  
## Issue Details  
The next version of Linux Fedora will be [Fedora 36][1]. On [Jan 19th, 2022][1], the Fedora Release Engineering team has [started a mass-rebuild of all Fedora package][2], to check if they all compile using the new GCC compiler version 12 and glibc version 2.35.  
  
[1]: https://fedorapeople.org/groups/schedule/f-36/f-36-key-tasks.html  
[2]: https://lists.fedoraproject.org/archives/list/devel-announce@lists.fedoraproject.org/thread/CQBJELILIXZRFQQEOHQLJ26S5GK74PJK/  
  
And [the package CGAL-5.4-0.2.beta1.fc36 does not compile with the new toolchain][fail]. The error comes from internals of Boost Multiprecision, and the failure is only on the Fedora secondary architecture PowerPC64le.  
  
Here is the failing task: [Koji task #81462801][fail]. Here is the failing build log: https://kojipkgs.fedoraproject.org//work/tasks/2947/81462947/build.log  
  
[fail]: https://koji.fedoraproject.org/koji/taskinfo?taskID=81462801  
  
## Possible Fix  
  
The error is that `/usr/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp` from Boost Multiprecision, version 1.76, test the presence of x86 intrinsics with:  
```c++  
#ifdef __has_include  
#if __has_include(<immintrin.h>)  
#define BOOST_MP_HAS_IMMINTRIN_H  
#endif  
#endif  
```  
and GCC version 12 has added the file `/usr/lib/gcc/ppc64le-redhat-linux/12/include/immintrin.h`.  
  
There seems to be two options:  
  - either one can "trust" x86 intrinsics on PPC with Boost Multiprecision, and then `boost/multiprecision/cpp_int/intel_intrinsics.hpp` should define `NO_WARN_X86_INTRINSICS`,  
  - or `boost/multiprecision/cpp_int/intel_intrinsics.hpp` should explicitly test against PPC64.

---

## Comment 1

> Username: lrineau  
> Created at: 2022-01-24 16:10:11 UTC  
> Updated at: 2022-01-24 16:10:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1020265751  

I should mention that this issue actually block any update of CGAL on Fedora 36, even if that is only on the secondary architecture ppc64le.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-01-25 09:30:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1020982723  

Are you able to test to see if https://github.com/boostorg/multiprecision/pull/421 fixes this?  
  
Also if you're able, could you please file a gcc bug report - I don't believe toolchains should be shipping with headers which aren't #include-able.  It breaks the whole reason for having `__has_include` in the first place.  That particular ship has already sailed for msvc, but it would be a shame for gcc to join them (and gcc-12 is still pre-release?).

---

## Comment 3

> Username: lrineau  
> Created at: 2022-01-26 10:00:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1022043230  

> Are you able to test to see if #421 fixes this?  
  
I am trying. I have to inject that patch in the Fedora build system, for build of `boost`, and then trigger a new build of `CGAL` on top of that. That is not easy but I know how to proceed.  
  
> Also if you're able, could you please file a gcc bug report - I don't believe toolchains should be shipping with headers which aren't #include-able. It breaks the whole reason for having `__has_include` in the first place. That particular ship has already sailed for msvc, but it would be a shame for gcc to join them (and gcc-12 is still pre-release?).  
  
I do not think that it is a bug of gcc. The C++ reference web site, https://en.cppreference.com/w/cpp/preprocessor/include, warns about that case:  
> A `__has_include` result of `1` only means that a header or source file with the specified name exists. It does not mean that the header or source file, when included, would not cause an error or would contain anything useful. For example, on a C++ implementation that supports both C++14 and C++17 modes (and provides `__has_include` in its C++14 mode as a conforming extension), `__has_include(<optional>)` may be `1` in C++14 mode, but actually `#include <optional>` may cause an error.  
  
In this particular case, `immintrin.h` can be used on PPC64 if the macro `NO_WARN_X86_INTRINSICS` is defined. If you know the x86 intrinsics used by Boost MP can safely be used on PPC64 as well, that could be another way to fix the issue.

---

## Comment 4

> Username: lrineau  
> Created at: 2022-01-26 10:04:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1022046229  

> > Are you able to test to see if #421 fixes this?  
>   
> I am trying. I have to inject that patch in the Fedora build system, for build of `boost`, and then trigger a new build of `CGAL` on top of that. That is not easy but I know how to proceed.  
  
What is particularly unfortunate is that the `boost` source package at Fedora Linux is monolithic: its builds last for a few hours. Whereas Boost Multiprecision on itself is header-only.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2022-01-26 10:51:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1022083836  

> Whereas Boost Multiprecision on itself is header-only  
  
This will not help the situation now, but as things progress, Multirpecision is intended to go _standalone_ in 1.79 as Math also went standalone a few months ago. Maybe as progress continues, the build system can/could reduce its dependencies for Boost.Math and Boost.Multiprecision as it goes forward...? Might help later --- but not for this particular issue.

---

## Comment 6

> Username: sharkcz  
> Created at: 2022-01-26 12:45:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1022165280  

seems it's actually a gcc bug per recent discussion on Fedora devel mailing list - https://gcc.gnu.org/bugzilla/show_bug.cgi?id=104239

---

## Comment 7

> Username: lrineau  
> Created at: 2022-01-26 13:18:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1022189335  

> seems it's actually a gcc bug per recent discussion on Fedora devel mailing list - [gcc.gnu.org/bugzilla/show_bug.cgi?id=104239](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=104239)  
  
Thanks again, Dan (@sharkcz). It seems I misread the error message and it was not really about `NO_WARN_X86_INTRINSICS` not being defined.  
  
@jzmaddock @ckormanyos: Let's put the PR #421 on hold for a while, until the issue is fixed in gcc. Then I will re-trigger a build of CGAL-5.4-beta1 and see if the issue is actually already fixed.

---

## Comment 8

> Username: mglisse  
> Created at: 2022-01-27 16:24:06 UTC  
> Updated at: 2022-01-27 16:25:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1023405274  

> It seems I misread the error message and it was not really about `NO_WARN_X86_INTRINSICS` not being defined.  
  
I think it is about `NO_WARN_X86_INTRINSICS`. Jakub fixed a bug that happened to be visible at the same time but is unrelated to the use of `__has_include`. That won't change the _feature_ that including immintrin.h purposedly causes an error on ppc unless some macro is defined.

---

## Comment 9

> Username: sharkcz  
> Created at: 2022-01-31 12:00:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1025664903  

IMO there are/were 2 issues, first is about how the header files can or cannot be included, which got fixed in GCC. Then there is the warning/error on ppc64le about the use of -DNO_WARN_X86_INTRINSICS. It can be added to the compiler/preprocessor flags and the build then runs. But it still fails due  
```  
...  
/usr/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp: In function 'unsigned char boost::multiprecision::detail::addcarry_limb(unsigned char, boost::multiprecision::limb_type, boost::multiprecision::limb_type, boost::multiprecision::limb_type*)':  
/usr/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp:82:22: error: '_addcarry_u64' was not declared in this scope  
   82 |    return BOOST_JOIN(BOOST_MP_ADDC, 64)(carry, a, b, reinterpret_cast<cast_type*>(p_result));  
      |                      ^~~~~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp: In function 'unsigned char boost::multiprecision::detail::subborrow_limb(unsigned char, boost::multiprecision::limb_type, boost::multiprecision::limb_type, boost::multiprecision::limb_type*)':  
/usr/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp:92:22: error: '_subborrow_u64' was not declared in this scope  
   92 |    return BOOST_JOIN(BOOST_MP_SUBB, 64)(carry, a, b, reinterpret_cast<cast_type*>(p_result));  
      |                      ^~~~~~~~~~~~~  
...  
````  
Probably the ppc64le implementation of the x86 intrinsics is missing some bits.

---

## Comment 10

> Username: lrineau  
> Created at: 2022-01-31 12:39:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1025698136  

Indeed. I triggered a new build, using [`gcc-12.0.1-0.4.fc36`](https://bodhi.fedoraproject.org/updates/FEDORA-2022-7b82014be0) that fixes the bug [`target/104239`](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=104239). And the [build on `ppc64le` still fails](https://koji.fedoraproject.org/koji/taskinfo?taskID=82180776), with [this error log](https://kojipkgs.fedoraproject.org//work/tasks/776/82180776/build.log):  
```  
[ 92%] Building CXX object CMakeFiles/constrained_plus.dir/constrained_plus.cpp.o  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/immintrin.h:29,  
                 from /usr/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp:68,  
                 from /usr/include/boost/multiprecision/cpp_int/add_unsigned.hpp:10,  
                 from /usr/include/boost/multiprecision/cpp_int/add.hpp:12,  
                 from /usr/include/boost/multiprecision/cpp_int.hpp:2286,  
                 from /builddir/build/BUILDROOT/CGAL-5.4-0.2.beta1.fc36.ppc64le/usr/include/CGAL/boost_mp.h:33,  
                 from /builddir/build/BUILDROOT/CGAL-5.4-0.2.beta1.fc36.ppc64le/usr/include/CGAL/Number_types/internal/Exact_type_selector.h:27,  
                 from /builddir/build/BUILDROOT/CGAL-5.4-0.2.beta1.fc36.ppc64le/usr/include/CGAL/Exact_kernel_selector.h:26,  
                 from /builddir/build/BUILDROOT/CGAL-5.4-0.2.beta1.fc36.ppc64le/usr/include/CGAL/Filtered_kernel.h:22,  
                 from /builddir/build/BUILDROOT/CGAL-5.4-0.2.beta1.fc36.ppc64le/usr/include/CGAL/Exact_predicates_exact_constructions_kernel.h:21,  
                 from /builddir/build/BUILDROOT/CGAL-5.4-0.2.beta1.fc36.ppc64le/usr/share/CGAL/examples/Triangulation_2/constrained_plus.cpp:1:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/mmintrin.h:52:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
   52 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
      |  ^~~~~  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/immintrin.h:31:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/xmmintrin.h:54:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
   54 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
      |  ^~~~~  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/xmmintrin.h:79:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/mmintrin.h:52:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
   52 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
      |  ^~~~~  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/immintrin.h:33:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/emmintrin.h:56:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
   56 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
      |  ^~~~~  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/emmintrin.h:66:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/xmmintrin.h:54:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
   54 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
      |  ^~~~~  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/immintrin.h:35:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/pmmintrin.h:49:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this warning."  
   49 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this warning."  
      |  ^~~~~  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/pmmintrin.h:56:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/emmintrin.h:56:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
   56 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this error."  
      |  ^~~~~  
In file included from /usr/lib/gcc/ppc64le-redhat-linux/12/include/tmmintrin.h:44,  
                 from /usr/lib/gcc/ppc64le-redhat-linux/12/include/immintrin.h:37:  
/usr/lib/gcc/ppc64le-redhat-linux/12/include/pmmintrin.h:49:2: error: #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this warning."  
   49 | #error "Please read comment above.  Use -DNO_WARN_X86_INTRINSICS to disable this warning."  
      |  ^~~~  
```  
  
I will now try the PR #421...

---

## Comment 11

> Username: lrineau  
> Created at: 2022-02-02 13:49:29 UTC  
> Updated at: 2022-02-02 13:49:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1027959450  

@jzmaddock It took me some time but I eventually [managed to confirm that PR #421 fixes the build issue on ppc64le](https://copr.fedorainfracloud.org/coprs/rineau/boostmp-issue-419/build/3291094/).  
  
I have submitted a pull-request to Fedora `boost` package: https://src.fedoraproject.org/rpms/boost/pull-request/11. I hope it will soon be integrated.

---

## Comment 12

> Username: lrineau  
> Created at: 2022-02-04 10:05:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1029825607  

> @jzmaddock It took me some time but I eventually [managed to confirm that PR #421 fixes the build issue on ppc64le](https://copr.fedorainfracloud.org/coprs/rineau/boostmp-issue-419/build/3291094/).  
>   
> I have submitted a pull-request to Fedora `boost` package: [src.fedoraproject.org/rpms/boost/pull-request/11](https://src.fedoraproject.org/rpms/boost/pull-request/11). I hope it will soon be integrated.  
  
At Fedora, the patch was merged yesterday, and then a new build of Boost-1.76 (plus that patch) was produced, and I have managed to compile CGAL-5.4 on top of that. On the side of Fedora, everything works, now.  
  
I expected this PR to be merged into `develop`, so that it can be in Boost version 1.79.0 mid-April. *I wonder why Linux Fedora is stuck at Boost version 1.76.0 for now. That is strange.*

---

## Comment 13

> Username: lrineau  
> Created at: 2022-02-04 10:06:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1029826231  

> I expected this PR to be merged into `develop`, so that it can be in Boost version 1.79.0 mid-April. __  
  
Sorry, I meant PR #421.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2022-02-04 11:19:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/419#issuecomment-1029896194  

Done.
