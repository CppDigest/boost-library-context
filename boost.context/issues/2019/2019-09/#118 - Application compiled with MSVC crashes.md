# #118 - Application compiled with MSVC crashes [Closed]

> Username: admo  
> Created at: 2019-09-13 08:04:48 UTC  
> Updated at: 2019-09-13 10:32:08 UTC  
> Closed at: 2019-09-13 10:28:33 UTC  
> Url: https://github.com/boostorg/context/issues/118  

Hi.  
Given sample code:  
```cpp  
#include <boost/context/execution_context.hpp>  
  
namespace ctx = boost::context;  
  
auto foo(ctx::execution_context<void>&& sink){  
    return std::move(sink);  
}  
  
int main() {  
    ctx::execution_context<void> ectx{foo};  
}  
```  
  
Compiled with MSVC (19.22.27905 from Visual Studio 2019) and Boost 1.71.0 in Release mode crashes.  
  
Boost documentation states that [`/GL`](https://docs.microsoft.com/en-us/cpp/build/reference/gl-whole-program-optimization?view=vs-2019) flag should be disabled, but it is disabled by default, and has to be enabled explicitly. Setting [`/EHs`](https://docs.microsoft.com/en-us/cpp/build/reference/eh-exception-handling-model?view=vs-2019) does not solve issue either.  
  
IMHO it is caused by [`/O2`](https://docs.microsoft.com/en-us/cpp/build/reference/o1-o2-minimize-size-maximize-speed?view=vs-2019) flag which includes [`/Og`](https://docs.microsoft.com/en-us/cpp/build/reference/og-global-optimizations?view=vs-2019) flag. It is possible to [specify optimizations on a function-by-function basis](https://docs.microsoft.com/en-us/cpp/preprocessor/optimize?redirectedfrom=MSDN&view=vs-2019):  
```cpp  
#pragma optimize( "g", off )  
  
#include <boost/context/execution_context.hpp>  
  
namespace ctx = boost::context;  
  
auto foo(ctx::execution_context<void>&& sink){  
    return std::move(sink);  
}  
  
int main() {  
    ctx::execution_context<void> ectx{foo};  
}  
```  
  
With above `pragma` application works as expected.  
  
Could you consider adding this pragma in some header, or mention it in documentation?  
  
Please note if user put `#pragma optimize( "g", on )` application will crash, regardless disabling it by the library.

---

## Comment 1

> Username: olk  
> Created at: 2019-09-13 10:28:33 UTC  
> Url: https://github.com/boostorg/context/issues/118#issuecomment-531186286  

execution_context is deprecated and will be removed (boost-1.72)

---

## Comment 2

> Username: admo  
> Created at: 2019-09-13 10:32:08 UTC  
> Url: https://github.com/boostorg/context/issues/118#issuecomment-531187265  

@olk This issue is valid for `boost::context::fiber` also. Should I create new issue or you reopen this one?
