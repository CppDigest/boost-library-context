# #147 - Tests fail on gcc-4.8, clang-3.9 on Travis [Closed]

> Username: pdimov  
> Created at: 2017-10-13 11:46:20 UTC  
> Updated at: 2017-11-08 05:31:42 UTC  
> Closed at: 2017-11-06 07:09:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/147  

See https://travis-ci.org/boostorg/boost/jobs/287409126 and https://travis-ci.org/boostorg/boost/jobs/287409127  
  
Not sure if this is relevant, but in any case.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-11-01 19:19:39 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-341211174  

Many failures have been fixed, but one remains:  
  
```  
testing.capture-output bin.v2/libs/fiber/test/test_topology.test/gcc-gnu-4.8/release/cxxstd-11-iso/link-static/threadapi-pthread/threading-multi/test_topology.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "Boost.Fiber: numa topology test suite/_test_topology": std::regex_error: regex_error  
libs/fiber/test/test_topology.cpp(21): last checkpoint  
*** 1 failure is detected in the test module "Master Test Suite"  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 2

> Username: olk  
> Created at: 2017-11-06 07:09:15 UTC  
> Updated at: 2017-11-06 07:09:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342063868  

boost.fiber doesn't use std::regex - must be a bug of gcc-4.8 (4.9 and later doesn't show this error)

---

## Comment 3

> Username: pdimov  
> Created at: 2017-11-06 13:44:40 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342152371  

Pretty mysterious how  
  
```  
    BOOST_CHECK( ! boost::fibers::numa::topology().empty() );  
```  
  
can result in a `std::regex_error`, yes. Probably not worth it to investigate though. :-)

---

## Comment 4

> Username: olk  
> Created at: 2017-11-06 14:04:46 UTC  
> Updated at: 2017-11-06 14:05:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342157684  

I've had changed the unit-test to:  
```  
bool empty = boost::fibers::numa::topology().empty();  
BOOST_CHECK( ! empty);  
```  
  
Should be in branch master (bac1125f0e73243ef4827c81453da2a042cfff75) and develop ?!

---

## Comment 5

> Username: pdimov  
> Created at: 2017-11-06 14:16:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342160999  

You're right, my local repo was behind. The latest fiber does indeed contain what you say. It still fails with  
  
```  
testing.capture-output bin.v2/libs/fiber/test/test_topology.test/gcc-gnu-4.8/release/cxxstd-11-iso/link-static/threadapi-pthread/threading-multi/test_topology.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "Boost.Fiber: numa topology test suite/_test_topology": std::regex_error: regex_error  
*** 1 failure is detected in the test module "Master Test Suite"  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
that is, the exception is thrown by `boost::fibers::numa::topology()`.  
  
Looking at https://github.com/boostorg/fiber/blob/develop/src/numa/linux/topology.cpp#L42, you do use `std::regex`, so that's one mystery fewer. :-)

---

## Comment 6

> Username: pdimov  
> Created at: 2017-11-06 14:18:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342161438  

Would you like me to enable Travis here and add a `.travis.yml` file?

---

## Comment 7

> Username: olk  
> Created at: 2017-11-06 14:19:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342161820  

Yes, would be nice (I've never used travis so far).

---

## Comment 8

> Username: pdimov  
> Created at: 2017-11-06 15:22:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342181363  

https://travis-ci.org/boostorg/fiber/builds/298010277  
  
Looks like a problem with `<regex>` on libstdc++-4.8.

---

## Comment 9

> Username: olk  
> Created at: 2017-11-06 16:30:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342204226  

OK. My first guess was that something with the BOOST_CHECK macro was going wrong (I forgot the code evaluating the NUMA part on Linux uses std::regex ... the code was written long time ago).  
  
I'm using Arch Linux, so I've the newest gcc and I'm too lazy to setup a qemu for gcc-4.8.  
  
Do you agree, that we don't fix it?

---

## Comment 10

> Username: pdimov  
> Created at: 2017-11-06 16:40:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342207515  

Boost.Config says  
  
```  
#if (BOOST_LIBSTDCXX_VERSION < 40900) || !defined(BOOST_LIBSTDCXX11)  
// Although <regex> is present and compilable against, the actual implementation is not functional  
// even for the simplest patterns such as "\d" or "[0-9]". This is the case at least in gcc up to 4.8, inclusively.  
#  define BOOST_NO_CXX11_HDR_REGEX  
#endif  
```  
  
so that's that. You could disable `test_topology.cpp` in the Jamfile, although the correct condition (`requires cxx11_hdr_regex` but only on Linux) is a bit hard to express. `<toolset>gcc-4.8:<build>no` is always an option, I suppose.

---

## Comment 11

> Username: olk  
> Created at: 2017-11-06 16:41:58 UTC  
> Updated at: 2017-11-06 16:43:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342208038  

Ty, I'll use the defect feature macro.

---

## Comment 12

> Username: pdimov  
> Created at: 2017-11-07 11:06:31 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342449675  

Do you support OS X? It doesn't seem to work. https://travis-ci.org/boostorg/fiber/builds/298295344

---

## Comment 13

> Username: olk  
> Created at: 2017-11-07 11:37:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342456596  

I don't have access to a MacOS X system - the error is caused because ucontext is deprecated by POSIX but most systems still keep it. Seams that Apple has skipped the support or I've not added the right code to enable it (_XOPEN_SOURCE_).

---

## Comment 14

> Username: pdimov  
> Created at: 2017-11-07 11:43:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342457884  

`ucontext` just causes warnings, but things fail, for different reasons on the different Xcode versions.  
  
If OS X is unsupported, I'll remove it from Travis, as there's no reason to keep testing it.

---

## Comment 15

> Username: olk  
> Created at: 2017-11-07 12:57:23 UTC  
> Updated at: 2017-11-07 12:58:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342474208  

OS X is supported for the assembler implementation, but ucontext (_native unit-tests) do fail.  
It seams not so easy to express the negation of OS X in the Jamfile for the unit-tests.  
  
I've something like below in mind:  
```  
test-suite native :  
[ run test_fiber_post.cpp :  
    : :  
    <conditional>@configure-impl  
    [ requires cxx11_auto_declarations  
               cxx11_constexpr  
               cxx11_defaulted_functions  
               cxx11_final  
               cxx11_hdr_mutex  
               cxx11_hdr_thread  
               cxx11_hdr_tuple  
               cxx11_lambdas  
               cxx11_noexcept  
               cxx11_nullptr  
               cxx11_rvalue_references  
               cxx11_template_aliases  
               cxx11_thread_local  
               cxx11_variadic_templates   
               ! mac_os_x]  
    : test_fiber_post_native ]  
```

---

## Comment 16

> Username: pdimov  
> Created at: 2017-11-07 13:23:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342480515  

To disable something on OS X, you probably need to use `<conditional>` that returns `<build>no` depending on `<target-os>`.

---

## Comment 17

> Username: olk  
> Created at: 2017-11-07 18:23:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342575178  

pushed - <target-os>macosx seams not to be right, I've used <target-os>darwin instead

---

## Comment 18

> Username: pdimov  
> Created at: 2017-11-08 00:08:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342666218  

There's still  
  
```  
testing.capture-output bin.v2/libs/fiber/test/test_topology.test/clang-darwin-darwin-4.2.1/release/cxxstd-11-iso/link-static/threadapi-pthread/threading-multi/test_topology.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location:0: fatal error: in "Boost.Fiber: numa topology test suite/_test_topology": boost::fibers::fiber_error: boost fiber: topology() not supported: Function not implemented  
*** 1 failure is detected in the test module "Master Test Suite"  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
Rest seems to work on xcode 8, 8.1; the others are still pending.

---

## Comment 19

> Username: olk  
> Created at: 2017-11-08 05:31:30 UTC  
> Updated at: 2017-11-08 05:31:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/147#issuecomment-342715171  

Right, I forgott that Mac OS X doesn't support NUMA - unit-test is disabled now.  
Ty.
