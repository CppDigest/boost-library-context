# #116 - execution_context destructor crashes when using msvc [Closed]

> Username: admo  
> Created at: 2019-08-29 11:06:16 UTC  
> Updated at: 2019-08-29 16:44:50 UTC  
> Closed at: 2019-08-29 16:42:50 UTC  
> Url: https://github.com/boostorg/context/issues/116  

Hi,  
Given:  
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
  
crashes when ectx is going to be destroyed. printf-like-debug session shows that it happens in `boost::context::detail::ecv2_context_unwind` right after throwing `boost::context::detail::forced_unwind`.  
  
Environment details:  
 * OS: Windows 10 1903 (build 18362.295)  
 * Toolset: msvc 14.22.27905  
 * Compiler version: 19.22.27905  
 * Boost version: precompiled boost 1.71.0 downloaded from [here](https://dl.bintray.com/boostorg/release/1.71.0/binaries/boost_1_71_0-msvc-14.2-64.exe)  
 * C++ standard: 17.  
  
However, it also crashes in other configurations like msvc 14.16.27023, boost 1.68.0 (precompiled and built on target machine).  
  
The above example works excellent on Linux hosts.  
  
I have not found any information about compatibility `boost::context` with msvc toolset, so I assumed that it's compatible. Am I right?  
  
Are you aware of this bug?  
  
**EDIT**  
When I compile in debug mode (use boost debug version) application does not crash.  
  
**EDIT2**  
When I change `/O2` compiler flag to `/O1` flag, then application does not crash.  
  
**EDIT3**  
`/O2` is equivalent to `/Og /Oi /Ot /Oy /Ob2 /GF /Gy`. When I use equivalent instead `/O2`, but without `/Og` then application does not crash. It seems that `/Og` causes crash.  
  
**EDIT4**  
After reading documentation carefully I’ve found that this issue is already known and addressed.
