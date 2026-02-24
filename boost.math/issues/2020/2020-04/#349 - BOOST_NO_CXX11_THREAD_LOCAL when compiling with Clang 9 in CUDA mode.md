# #349 - BOOST_NO_CXX11_THREAD_LOCAL when compiling with Clang 9 in CUDA mode [Open]

> Username: invexed  
> Created at: 2020-04-29 12:44:19 UTC  
> Updated at: 2020-04-30 07:32:17 UTC  
> Url: https://github.com/boostorg/math/issues/349  

```__has_feature(cxx_thread_local)``` resolves to false when compiling with Clang 9 in CUDA mode, which in turn triggers a C++03 deprecation warning. Is there any plan to support Clang's CUDA mode once C++03 support is dropped? As far as I know ```cxx_thread_local``` is the only C++11 feature it doesn't support.  
  
https://bugs.llvm.org/show_bug.cgi?id=45732

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-04-29 13:14:38 UTC  
> Url: https://github.com/boostorg/math/issues/349#issuecomment-621196380  

Could you post the compile flags and a mwe you're using when you hit this? I'd like to reproduce . . .

---

## Comment 2

> Username: invexed  
> Created at: 2020-04-29 14:39:55 UTC  
> Url: https://github.com/boostorg/math/issues/349#issuecomment-621254729  

This is a little tricky as I'm using a compiler plugin which hides the real Clang invocation, but I'll do my best. All it takes is to include a header:  
  
```main.cpp```  
```cpp  
#include <boost/math/distributions/normal.hpp>  
  
int main()  
{  
      
}  
```  
  
```CMakeLists.txt``` (after removing lines specific to compiler plugin)  
```cmake  
cmake_minimum_required(VERSION 3.17)  
  
project(clang-cuda-thread-local)  
  
set(BOOST_ROOT ${CMAKE_SOURCE_DIR}/external/boost_1_73_0)  
find_package(Boost 1.73.0 REQUIRED)  
  
add_executable(test)  
target_sources(test PRIVATE  
    main.cpp  
)  
target_compile_options(test PRIVATE  
    -std=c++17  
)  
target_link_libraries(test PRIVATE  
    Boost::headers  
)  
```  
Clang version is ```9.0.1```, CUDA is ```10.0.130```, the problem occurs for both debug and release builds and I'm targeting the ```sm_50``` CUDA arch. ```-std=c++17``` is the only option I'm specifying manually, but as I mentioned, the compiler plugin takes care of the CUDA-related options behind the scenes. I imagine they are ```-x cuda --cuda-gpu-arch=sm_50 -L<CUDA install path>/<lib64 or lib> -lcudart -ldl -lrt -pthread``` or similar.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-04-29 16:42:18 UTC  
> Url: https://github.com/boostorg/math/issues/349#issuecomment-621329229  

Thanks for the heads up - this is one feature I really would like to rely on, as it greatly simplifies initializing static data (coefficients for approximations etc).  Now that said, for float/double which are presumably the only types you'll be using on CUDA, this is a non-issue, so thinking out loud here, one option might be to conditionally static assert that static initialization is safe when the feature is unavailable.  
  
Note that we already have function (hypergeometric) which already unconditionally requires this.... we would also need to figure out some testing methodology.  
  
But I can see that there may be platforms that will never support this, so maybe we should try to find a way forward.  Either way nothing will change immediately.

---

## Comment 4

> Username: invexed  
> Created at: 2020-04-30 07:32:17 UTC  
> Url: https://github.com/boostorg/math/issues/349#issuecomment-621665204  

Thanks for the reply. Yes, I can understand why you'd want to rely on ```thread_local```, the trouble is that it isn't available even in host (CPU) code when compiling in CUDA mode. It makes sense that this wouldn't be available in device code but I would have thought it should be available in host code. It'll be interesting to see what the Clang developers have to say about it; I'll get in touch with Justin who worked on the initial contribution to LLVM if I don't get a response on the bug tracker. It would be nice if we didn't have to do anything on our side, and we're not pressed for time.
