# #527 - Boost failed with error C2760 when build with permissive- by MSVC on windows [Closed]

> Username: QuellaZhang  
> Created at: 2018-11-09 10:00:44 UTC  
> Updated at: 2018-11-26 18:25:38 UTC  
> Closed at: 2018-11-26 18:25:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/527  

We tried to build and run geometry test for Boost on Windows. It failed to build due to the error C2760. Could you please help take a look at this? Thank you!  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3. set _CL_=/DNOMINMAX /wd4643 /permissive-  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
7. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\geometry\test  
  
**Failures:**  
  
```  
projection.cpp  
.\boost/geometry/srs/projections/proj/fouc_s.hpp(128): error C2760: syntax error: unexpected token 'constant', expected ')'  
.\boost/geometry/srs/projections/proj/healpix.hpp(728): error C2760: syntax error: unexpected token 'constant', expected ')'  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2018-11-09 11:18:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/527#issuecomment-437330363  

You don't specify which version of Boost you are using.  
Let's assume it is the current `master` branch, or latest release which is close to the branch.  
  
That is one of those cryptic compiler errors difficult to reason about, but it seems plausible, the error is due to undefined `BOOST_THROW_EXCEPTION` macro, here  
  
https://github.com/boostorg/geometry/blob/d6700a8865aa24301c9f5c9f1ed0f0b34a4cdc82/include/boost/geometry/srs/projections/proj/fouc_s.hpp#L127-L130  
  
and here  
  
https://github.com/boostorg/geometry/blob/bf62e05e235098ced18c75ba3d40aefd779648cf/include/boost/geometry/srs/projections/proj/healpix.hpp#L727-L730  
  
That would mean,  `BOOST_THROW_EXCEPTION` is undefined due to missing `#include <boost/throw_exception.hpp>`.  
  
Could you add this `#include` to `fouc_s.hpp` and `healpix.hpp`, somewhere before `namespace boost { namespace geometry` and see if it helps?

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-11-09 12:49:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/527#issuecomment-437350165  

It's probably caused by the compiler assuming that member access and less comparisons is template instantiation (`proj_parm.n<0.||proj_parm.n>`). Could you try to put parentheses around the comparisons and see if it works?  
  
    if ((proj_parm.n < 0.) || (proj_parm.n > 1.))

---

## Comment 3

> Username: QuellaZhang  
> Created at: 2018-11-12 06:24:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/527#issuecomment-437770126  

Thank you very much! And we have been using the latest version of master branch, I found this issue in version 6b17c72.  
  
I apply two patches separately baised on what you said. According to mloskot, Boost got same errors like above, according to awulkiew, Boost built and tested succeed. I attachment those two patches.  
  
[geometry_mloskot.patch.txt](https://github.com/boostorg/geometry/files/2570730/geometry_mloskot.patch.txt)  
[geometry_awulkiew.patch.txt](https://github.com/boostorg/geometry/files/2570729/geometry_awulkiew.patch.txt)

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-11-20 21:09:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/527#issuecomment-440430334  

Thanks for testing. I pushed workaround into develop.

---

## Comment 5

> Username: awulkiew  
> Created at: 2018-11-26 18:25:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/527#issuecomment-441744921  

Pushed to master for 1.69.
