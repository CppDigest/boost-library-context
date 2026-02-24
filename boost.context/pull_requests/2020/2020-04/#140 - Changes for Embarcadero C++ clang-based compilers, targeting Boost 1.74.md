# #140 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-25 03:06:09 UTC  
> Updated at: 2020-06-28 15:05:45 UTC  
> Merged at: 2020-06-28 15:05:44 UTC  
> Closed at: 2020-06-28 15:05:44 UTC  
> Url: https://github.com/boostorg/context/pull/140  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-13 16:47:35 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-628113109  

The Travis failure says:  
  
"Reading package lists...  
Building dependency tree...  
Reading state information...  
Some packages could not be installed. This may mean that you have  
requested an impossible situation or if you are using the unstable  
distribution that some required packages have not yet been created  
or been moved out of Incoming.  
The following information may help to resolve the situation:  
  
The following packages have unmet dependencies:  
 clang-5.0 : Depends: libjsoncpp0 (>= 0.6.0~rc2) but it is not installable  
E: Unable to correct problems, you have held broken packages."  
  
This has nothing to do with this PR.

---

## Comment 2

> Username: olk  
> Created_at: 2020-05-13 17:31:25 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-628137305  

It is a problem of the build environment.

---

## Comment 3

> Username: olk  
> Created_at: 2020-05-14 06:06:10 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-628407750  

Is your pull request complete (assembler and configuration in the Jamfile) and do the unit-tests pass?

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-05-14 14:19:17 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-628667472  

The pull request is complete.

---

## Comment 5

> Username: olk  
> Created_at: 2020-05-14 18:33:52 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-628814510  

But did you run the unit-tests and they succeed?

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-05-14 21:43:04 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-628902040  

I do not know what you mean by "run the unit tests". Is not that what your CI tests are all about ? Do you mean did I test the Embarcadero C++ clang-based compilers ? I was able to build Boost context  with Embarcadero C++ clang-based compilers.

---

## Comment 7

> Username: olk  
> Created_at: 2020-05-15 04:35:57 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-629020679  

In sub directory _test_ execute b2. This runs the tests that CI executes too.

---

## Comment 8

> Username: eldiener  
> Created_at: 2020-05-16 23:34:21 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-629719639  

The Embarcadero clang-based compilers, which are currewntly based off of clang-5.0, have BOOST_NO_CXX11_THREAD_LOCAL defined since the clang--5.0 compiler, and therefore the Embarcadero compiler, put out an error whenever a thread local variable is a non-trivial class.

---

## Comment 9

> Username: olk  
> Created_at: 2020-06-26 05:19:56 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-649974287  

Why not test for BOOST_NO_CXX11_THREAD_LOCAL and use __thread etc.?

---

## Comment 10

> Username: eldiener  
> Created_at: 2020-06-26 20:24:53 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-650383673  

I know that the config test for thread local for Embarcadero clang-based compilers fails. I am not sure want you want me to do vis-a-vis context.

---

## Comment 11

> Username: olk  
> Created_at: 2020-06-28 07:01:25 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-650707853  

I suggest you wrap `thread_local` by testing for BOOST_NO_CXX11_THREAD_LOCAL.  
If BOOST_NO_CXX11_THREAD_LOCAL is not defined use `thread_local` otherwise use the compiler specific TLS specifier (for GCC it's `__thread`).

---

## Comment 12

> Username: eldiener  
> Created_at: 2020-06-28 14:25:36 UTC  
> Url: https://github.com/boostorg/context/pull/140#issuecomment-650768482  

BOOST_NO_CXX11_THREAD_LOCAL is defined for the Embarcadero clang-based compilers because it fails the thread local test in Boost.Config. I have reported this as a bug to Embarcadero ( https://quality.embarcadero.com/browse/RSP-28277?filter=-2 ). There is no compiler specific solution to this for the Embarcadero clang-based compilers until they fix their bug. Nonetheless I think it would be good to merge this PR so when they fix the bug context should work for the Embarcadero clang-based compilers. This PR does not affect any other compiler other than the Embarcadero clang-based compilers.

---
