# #659 - Geometry "builtins" normalize and fast_normalize not declared [Closed]

> Username: kenba  
> Created at: 2016-09-20 10:33:03 UTC  
> Updated at: 2016-09-23 14:18:32 UTC  
> Closed at: 2016-09-23 14:13:34 UTC  
> Url: https://github.com/boostorg/compute/issues/659  

The following code to calculate the normals of a vector of points fails to compile:  
  
```  
#include <boost/compute/functional/math.hpp>  
#include <boost/compute/functional/geometry.hpp>  
#include <boost/compute/lambda.hpp>  
  
namespace compute = boost::compute;  
  
...  
  // Calculate normals on the device  
  using namespace boost::compute::lambda;  
  boost::compute::transform(device_points.begin(), device_points.end() -1,  
                            device_points.begin() +1, device_normals.begin(),  
                            normalize(cross(_1, _2)), queue);  
```  
  
with:  
  
```  
error: 'normalize' was not declared in this scope  
                            normalize(cross(_1, _2)), queue);  
                                                  ^  
```  
  
Replacing `normalize` with `fast_normalize` fails with the same error, as does the "proper" English spellings of `normalise` and `fast_normalise` ; ).  However, replacing `normalize` with `cross` compiles and runs fine, but calculates the wrong result (obviously!).  
  
I can see the builtins for `normalize` and `fast_normalize` declared in `geometry.hpp` along with `cross`, etc.:  
  
```  
namespace boost {  
namespace compute {  
BOOST_COMPUTE_DECLARE_BUILTIN_FUNCTION(cross, T (T, T), class T)  
...  
BOOST_COMPUTE_DECLARE_BUILTIN_FUNCTION(normalize, T (T), class T)  
BOOST_COMPUTE_DECLARE_BUILTIN_FUNCTION(fast_normalize, T (T), class T)  
} // end compute namespace  
} // end boost namespace  
```  
  
But using `boost::compute::normalize(cross(_1, _2)` gives:  
  
```  
error: missing template arguments before '(' token  
                             boost::compute::normalize(cross(_1, _2)), queue);  
                                                     ^  
```  
  
I've got a workaround with a kernel function using `boost::compute::function` , but it'd be simpler (and it may be quicker) to use `normalize`...  
  
I'm compiling under Windows 10 using `MinGw` version: 5.3.0 and `compute` from `boost`: version 1.61

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-09-20 12:09:26 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248282751  

I am not sure, and I'll check it when I am home, but 'normalize' might not be defined for lambdas. Thanks for info.

---

## Comment 2

> Username: kenba  
> Created at: 2016-09-20 12:57:32 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248293471  

Thank you Jakub.  
FYI neither `normalize` or `fast_normalize` are defined in `functional.hpp` . Simply adding:  
  
```  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(normalize)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(fast_normalize)  
```  
  
to functional.hpp` gave the new error:  
  
```  
error: no matching function for call to 'normalize(const type)'  
                             normalize(cross(_1, _2)), queue);  
                                                   ^  
```

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-09-20 13:03:26 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248295007  

`normalize` is an unary function.

---

## Comment 4

> Username: kenba  
> Created at: 2016-09-20 13:56:05 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248308362  

D'oh of course it is! Thank you Jakub.   
Adding:  
  
```  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(normalize)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(fast_normalize)  
```  
  
to `functional.hpp` fixed the issue.

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-09-20 14:27:34 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248317587  

Sure, I am glad I could help. I'll push that fix later today.

---

## Comment 6

> Username: kenba  
> Created at: 2016-09-21 07:42:12 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248535841  

Sorry about this Jakub but I thought I'd better test another function as a lambda and found that `atan2` is missing, so I added:  
  
```  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(atan2)  
```  
  
to `functional.hpp` which fixed it.  
  
But I then thought I'd better check if there are any other missing lambdas and found the following...  
From `functional/math.cpp`:  
  
```  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(acosh)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(acospi)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(asinh)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(asinpi)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(atanh)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(atanpi)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(cbrt)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(ceil)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(cosh)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(cospi)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(erf)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(erfc)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(expm1)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(fabs)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(floor)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(ilogb)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(lgamma)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(log1p)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(logb)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(rint)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(rootn)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(sinh)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(sinpi)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(tanh)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(tanpi)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(tgamma)  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(trunc)  
  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(atan2pi)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(copysign)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(fdim)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(fmax)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(fmin)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(fmod)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(hypot)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(nextafter)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(remainder)  
```  
  
From `functional/geometry.cpp`:  
  
```  
BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(fast_length)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(fast_distance)  
```  
  
From `functional/relational.cpp`:  
  
```  
BOOST_COMPUTE_LAMBDA_WRAP_BOOLEAN_UNARY_FUNCTION(isnormal)  
BOOST_COMPUTE_LAMBDA_WRAP_BOOLEAN_UNARY_FUNCTION(isordered)  
BOOST_COMPUTE_LAMBDA_WRAP_BOOLEAN_UNARY_FUNCTION(isunordered)  
BOOST_COMPUTE_LAMBDA_WRAP_BOOLEAN_UNARY_FUNCTION(signbit)  
```  
  
These are also from `functional/relational.cpp` but they may need a new macro:  
  
```  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(isequal)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(isnotequal)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(isgreater)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(isgreaterequal)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(isless)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(islessequal)  
BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION_ST(islessgreater)  
```  
  
Please note that I've only tested the macro for `atan2`...

---

## Comment 7

> Username: jszuppe  
> Created at: 2016-09-21 07:58:33 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248539070  

Yes, thank you, I have noticed it too.  I have a commit with all the functions you listed here (with tests) waiting. In https://github.com/boostorg/compute/pull/660, I asked Kyle Lutz if he skipped them on purpose, or just simply implemented the common ones and went to the next issue. If there was no reason behind this, they all should be soon in the `develop` branch.  Thanks again!

---

## Comment 8

> Username: kenba  
> Created at: 2016-09-21 08:06:46 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248540767  

Thank you Jakub.

---

## Comment 9

> Username: jszuppe  
> Created at: 2016-09-21 13:37:50 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248614651  

I'll send it (my commit) to a branch on my fork later today and give you a  
link to it here, because I've found out that not all built-in functions can  
be wrapped using existing macros (the result type is sometimes wrong).  
  
21 wrz 2016 10:06 "Ken Barker" notifications@github.com napisał(a):  
  
> Thank you Jakub.  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/compute/issues/659#issuecomment-248540767,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/ACmCBFeaJkRldYVyWyDK5ff3O0TLRSzpks5qsOWYgaJpZM4KBeB5  
> .

---

## Comment 10

> Username: kenba  
> Created at: 2016-09-21 14:31:58 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248630447  

No problem Jakub. The only ones that I really care about are `normalize` and `atan2`.   
  
It would be great if they could make it into `boost` 1.62, but I appreciate that time's a bit tight for that and I have workarounds for both issues after all...

---

## Comment 11

> Username: jszuppe  
> Created at: 2016-09-21 18:37:07 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248702956  

I'm not sure if that's possible. 1.62 release is going to be closed today.

---

## Comment 12

> Username: kenba  
> Created at: 2016-09-22 06:23:33 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-248822288  

No problem Jakub, I guessed as much so there's no rush.  
  
Since, you've submitted a pull request for #660, I recommend closing this issue and ensuring that all of the OpenCL builtins are wrapped under #660, even if that means creating some new macros.  
  
BTW I've written ternary functions that I'd like to use with `boost::compute::transform`. Is it possible to wrap kernel functions written using `boost::compute::function` to use with lambdas?

---

## Comment 13

> Username: jszuppe  
> Created at: 2016-09-22 22:27:21 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-249046502  

I pushed all my changes to #660, you can check them out. If you want to can close the issue, I don't have any authority here ;)  
  
As for combining lambdas and `boost::compute::function`:   
First, just to clarify, `boost::compute::function` is not a kernel function. It's a normal function that is passed to algorithms like `boost::compute::transform` to generate proper kernel (code is generated in the run-time, later it is cached, there are slides about this available in the net).   
Second, I think you can not wrap `boost::compute::function`s to use them with lambdas, but you can stack `boost::compute::transform_iterator`s like this:  
  
``` cpp  
// a = { 1, -2, 3, -4, 5 }  
boost::compute::copy(  
    boost::compute::make_transform_iterator(  
        boost::compute::make_transform_iterator(  
            a.begin(),  
            boost::compute::_1 + 3  
        ),  
        boost::compute::abs<int>()  
    ),  
    boost::compute::make_transform_iterator(  
        boost::compute::make_transform_iterator(  
            a.end(),  
            boost::compute::_1 + 3  
        ),  
        boost::compute::abs<int>()  
    ),  
    b.begin(),  
    queue  
);  
// b = { 4, 1, 6, 1, 8 }  
```  
  
However, I'll research wrapping custom function into lambdas. I see how that might be useful.

---

## Comment 14

> Username: kenba  
> Created at: 2016-09-23 14:13:34 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-249204083  

Thank you for clarifying `boost::compute::function` for me Jakub.  
I found [this](http://www.iwocl.org/wp-content/uploads/iwocl-2016-boost-compute.pdf) on the web, written by your good self. I assume that this is what you meant about "slides available on the net"...  
  
I've commented on your changes under #660, so I'll try to close this issue.  
Thank you for your prompt and comprehensive answers to this issue. And many thanks to yourself, Kyle and everybody else involved, for developing such an excellent library.

---

## Comment 15

> Username: jszuppe  
> Created at: 2016-09-23 14:18:32 UTC  
> Url: https://github.com/boostorg/compute/issues/659#issuecomment-249205318  

There are also presentations made by Kyle: [CppNow15](https://github.com/boostcon/cppnow_presentations_2015/blob/master/files/Boost.ComputeCxxNow2015.pdf) ([video](https://www.youtube.com/watch?v=q7oCblCtTT8)) and [GTC'15](http://on-demand.gputechconf.com/gtc/2015/presentation/S5488-Kyle-Lutz.pdf).
