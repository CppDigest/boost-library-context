# #28 - polygon/detail/voronoi_ctypes.h does not include <algorithm> but uses std::min ? [Closed]

> Username: dkrejsa  
> Created at: 2019-04-10 19:52:22 UTC  
> Updated at: 2019-05-05 12:13:10 UTC  
> Closed at: 2019-05-05 12:13:10 UTC  
> Url: https://github.com/boostorg/polygon/issues/28  

Hi,  
Running the Boost 1.69 polygon test on VxWorks 7, four voronoi-related tests fail to compile due to the use of std::min  in voronoi_ctypes.hpp, which does not include the  
  
    <algorithm>  
  
header.  
  
Adding  
  
    #include <algorithm>  
  
after the  
  
    #include <vector>  
  
line appears to fix the problem.  
This is using the Dinkum-derived C++ libraries for VxWorks, and building with clang 7.0.0.1.  
  
I'm guessing that in other environments  
  
    <algorithm>  
  
is getting included somehow, but is  
there a reason that voronoi_ctypes.hpp should not include it directly?

---

## Comment 1

> Username: asydorchuk  
> Created at: 2019-05-05 12:13:07 UTC  
> Url: https://github.com/boostorg/polygon/issues/28#issuecomment-489420801  

Thanks for the report! Fixed.
