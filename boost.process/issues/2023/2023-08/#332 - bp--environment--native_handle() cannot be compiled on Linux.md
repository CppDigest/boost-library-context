# #332 - bp::environment::native_handle() cannot be compiled on Linux [Open]

> Username: AJIOB  
> Created at: 2023-08-06 06:55:18 UTC  
> Updated at: 2023-08-08 00:39:14 UTC  
> Url: https://github.com/boostorg/process/issues/332  

Code example to reproduce the issue:  
  
```c  
#include <boost/process.hpp>  
  
#include <iostream>  
  
namespace bp = boost::process;  
  
int main() {  
    auto nativeEnv = boost::this_process::environment();  
    auto nativeEnvHandle = nativeEnv.native_handle();  
    std::cout << *nativeEnvHandle;  
  
    bp::environment env = nativeEnv;  
    // should also work, but don't  
    auto envHandle = env.native_handle();  
    std::cout << *envHandle;  
  
    return 0;  
}  
```  
  
GodBolt link to reproduce the issue: https://godbolt.org/z/cT7ovGv68  
  
Affected library versions: boost 1.77.0, boost 1.82.0 (another versions were not tested).  
Affected compilers: gcc 6.3.0, gcc 13.2, clang 16.0.0 (another versions were not tested).  
  
Compiler flags: `-std=c++11 -O2`  
  
Compiler output (gcc 13.2):  
```log  
In file included from /opt/compiler-explorer/libs/boost_1_82_0/boost/process/environment.hpp:17,  
                 from /opt/compiler-explorer/libs/boost_1_82_0/boost/process/env.hpp:10,  
                 from /opt/compiler-explorer/libs/boost_1_82_0/boost/process.hpp:28,  
                 from <source>:1:  
/opt/compiler-explorer/libs/boost_1_82_0/boost/process/detail/posix/environment.hpp: In instantiation of 'Char** boost::process::detail::posix::basic_environment_impl<Char>::native_handle() const [with Char = char; native_handle_type = char**]':  
<source>:14:39:   required from here  
/opt/compiler-explorer/libs/boost_1_82_0/boost/process/detail/posix/environment.hpp:213:54: error: cannot convert 'const __gnu_cxx::__alloc_traits<std::allocator<std::__cxx11::basic_string<char> >, std::__cxx11::basic_string<char> >::value_type*' {aka 'const std::__cxx11::basic_string<char>*'} to 'boost::process::detail::posix::basic_environment_impl<char>::native_handle_type' {aka 'char**'} in return  
  213 |     native_handle_type native_handle() const {return &_data.front();}  
      |                                                      ^~~~~~~~~~~~~~  
      |                                                      |  
      |                                                      const __gnu_cxx::__alloc_traits<std::allocator<std::__cxx11::basic_string<char> >, std::__cxx11::basic_string<char> >::value_type* {aka const std::__cxx11::basic_string<char>*}  
Compiler returned: 1  
```  
  
Proposed solution: store the duplicate `vector<char*> _nativeData`. Every `char*` should point on the same element on `_data` vector. `_nativeData` should reload elements on every `_data` rebuilding or element (stored `std::string`) changing.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-08 00:39:14 UTC  
> Url: https://github.com/boostorg/process/issues/332#issuecomment-1668747252  

Well that's embarrassing. Pretty amazing that you're the first one to catch it though. I'll have a fix in the next release.
