# #566 Update tests compile options in Jamfile.v2 [Merged]

> Username: jszuppe  
> Created at: 2016-03-07 11:18:10 UTC  
> Updated at: 2016-03-10 21:13:25 UTC  
> Merged at: 2016-03-10 21:04:02 UTC  
> Closed at: 2016-03-10 21:04:02 UTC  
> Url: https://github.com/boostorg/compute/pull/566  

This is a fast fix for https://github.com/boostorg/compute/issues/563 (+ I've suppressed some other warning for MSVC that are just annoying) .   
  
I'll later remove lines  
  
```  
<toolset>gcc:<cxxflags>-Wno-deprecated-declarations # Deprecated declarations in CL/cl.h  
<toolset>clang:<cxxflags>-Wno-deprecated-declarations # Deprecated declarations in CL/cl.h  
<toolset>darwin:<cxxflags>-Wno-deprecated-declarations # Deprecated declarations in CL/cl.h  
<toolset>msvc:<cxxflags>/wd4996 # Deprecated declarations in CL/cl.h  
```  
  
and suppress deprecated declarations warning (related to CL/cl.h) in the code.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-03-07 22:35:39 UTC  
> Url: https://github.com/boostorg/compute/pull/566#issuecomment-193486982  

- I've suppressed those deprecated declarations warnings in the code.   
- I have also added OpenCL link flag for FreeBSD. If you take a look on [Boost regression](http://www.boost.org/development/tests/develop/developer/compute.html) you'll see that for some FreeBSD there is a link error, not a compilation error => there is "CL/cl.h", so maybe there is OpenCL implementation too.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-03-08 04:55:57 UTC  
> Url: https://github.com/boostorg/compute/pull/566#issuecomment-193609097  

Cool! I like the idea of doing this in the code rather than build system files.  
  
However, I think we can do this in a slightly cleaner way. Currently this approach requires defining some global macros (like `GCC_DIAG_ON`) in our headers and then surrounding our potentially-deprecated OpenCL calls with them (which is quite a few lines of boilerplate which may also have to be modified/updated in the future as compilers change).  
  
A nicer approach may be defining a pair of headers, `disable_warnings.hpp` which disable all the diagnostics and and `enable_warnings.hpp` which re-enables the diagnostics. The disable header would be included at the top of each of our headers that call (potentially deprecated) OpenCL functions, and the enable header would be included at the bottom to reenable the warnings (as to not affect any other header/source files which include our headers). There are a few examples of this in Boost, for instance Boost.Random and Boost.Endian both take similar approaches.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-03-08 21:24:31 UTC  
> Url: https://github.com/boostorg/compute/pull/566#issuecomment-193973968  

I like this idea with a pair of headers, but disabling warning for the whole file? IIRC [command_queue.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/command_queue.hpp) has ~1700 lines, it's big enough that in the future deprecated OpenCL function can be used by a mistake. With warning disabled for the whole file, there would be no sign of that.  
  
I can reduce it to two macros (one before, one after deprecated OpenCL function) or to one macro (deprecated OpenCL function as an argument).

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-03-09 00:15:36 UTC  
> Url: https://github.com/boostorg/compute/pull/566#issuecomment-194033270  

Moreover, IMHO it's hard to notice that the warnings are disabled for the whole file and in this option with headers there is no information in the code why those warnings are disabled. What do you think?

---

## Comment 5

> Username: kylelutz  
> Created_at: 2016-03-09 04:10:49 UTC  
> Updated_at: 2016-03-09 04:11:38 UTC  
> Url: https://github.com/boostorg/compute/pull/566#issuecomment-194106469  

Yeah I agree, disabling them for the whole file isn't ideal. If we could get this down to a two-liner push-pop, or a one-liner function wrapper macro (similar to `BOOST_COMPUTE_ASSERT_CL_SUCCESS`) I think that would be better.  
  
Also make sure any macros you introduce in the public headers have the `BOOST_COMPUTE_` prefix to avoid collisions with other code.

---

## Comment 6

> Username: jszuppe  
> Created_at: 2016-03-09 14:13:06 UTC  
> Updated_at: 2016-03-09 18:06:35 UTC  
> Url: https://github.com/boostorg/compute/pull/566#issuecomment-194311520  

Unfortunately, one-liner function wrapper macro somehow does not work in `g++` (maybe related to [53431](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=53431); it works in clang 3.7), so I've done a two-liner.  
  
one-liner (does not work, not included):  
  
``` cpp  
// This does not work in g++ (4.8)  
  
// Suppress deprecated-declarations warnings for code  
#define BOOST_COMPUTE_DEPRECATED_DECLARATIONS_OFF(code) \  
    BOOST_COMPUTE_DIAG_OFF(deprecated-declarations, deprecated-declarations, 4996) \  
    code \  
    BOOST_COMPUTE_DIAG_ON(deprecated-declarations, deprecated-declarations, 4996)  
```  
  
two-liners usage:  
  
``` cpp  
BOOST_COMPUTE_DIAG_OFF(sign-compare, sign-compare, 4018);  
a < b  
BOOST_COMPUTE_DIAG_ON(sign-compare, sign-compare, 4018);  
  
// for deprecated declarations warning it's simplified:  
BOOST_COMPUTE_DISABLE_DEPRECATED_DECLARATIONS();  
.. // deprecated functions  
BOOST_COMPUTE_ENABLE_DEPRECATED_DECLARATIONS();  
```  
  
I want to add that macros related to suppressing deprecated declarations warning are also included in `opencl_test.cpp` because it should be free of any dependency on Boost.Compute.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2016-03-10 21:04:00 UTC  
> Url: https://github.com/boostorg/compute/pull/566#issuecomment-195048109  

Looks good! Thanks!

---
