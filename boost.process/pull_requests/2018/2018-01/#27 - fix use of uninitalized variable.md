# #27 fix use of uninitalized variable [Merged]

> Username: adrianimboden  
> Created at: 2018-01-06 12:41:30 UTC  
> Updated at: 2018-02-05 22:16:21 UTC  
> Merged at: 2018-02-05 22:16:21 UTC  
> Closed at: 2018-02-05 22:16:21 UTC  
> Url: https://github.com/boostorg/process/pull/27  

I started to use clang-tidy on my project and it detected the use of an uninitialized variable.  
I'm not sure if the changes I've done are really the solution, but it at least does change undefined behavior into exit_code = 0.  
  
This is a minimal example to see the clang-tidy warning:  
  
test.cpp:  
```  
#include <boost/process.hpp>  
int main() { boost::process::child proc{"prog"}; }  
```  
  
clang-tidy call:  
```  
$ git clone --recursive https://github.com/boostorg/boost  
$ clang-tidy -checks=clang-analyzer-core.CallAndMessage test.cpp -- clang++ --std=c++14 -I boost/libs/process/include/ -I boost/libs/config/include/ -I boost/libs/system/include/ -I boost/libs/filesystem/include/ -I boost/libs/core/include/ -I boost/libs/type_traits/include/ -I boost/libs/predef/include/ -I boost/libs/assert/include/ -I boost/libs/iterator/include/ -I boost/libs/mpl/include/ -I boost/libs/preprocessor/include/ -I boost/libs/static_assert/include/ -I boost/libs/detail/include/ -I boost/libs/smart_ptr/include/ -I boost/libs/throw_exception/include/ -I boost/libs/io/include/ -I boost/libs/container_hash/include/ -I boost/libs/optional/include/ -I boost/libs/algorithm/include/ -I boost/libs/range/include/ -I boost/libs/concept_check/include/ -I boost/libs/utility/include/ -I boost/libs/asio/include/ -I boost/libs/fusion/include/ -I boost/libs/move/include/ -I boost/libs/type_index/include/ -I boost/libs/integer/include/ -I boost/libs/winapi/include -I boost/libs/function/include/ -I boost/libs/bind/include/ -I boost/libs/tokenizer/include/  
  
boost/libs/process/include/boost/process/detail/child_decl.hpp:171:17: warning: 1st function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
                _exit_status->store(code);  
                ^  
/home/thingdust/src/test.cpp:3:50: note: Calling '~child'  
int main() { boost::process::child proc{"prog"}; }  
                                                 ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:95:13: note: Assuming the condition is true  
        if (_attached && !_exited() && running(ec))  
            ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:95:13: note: Left side of '&&' is true  
boost/libs/process/include/boost/process/detail/child_decl.hpp:95:13: note: Left side of '&&' is true  
boost/libs/process/include/boost/process/detail/child_decl.hpp:95:40: note: Calling 'child::running'  
        if (_attached && !_exited() && running(ec))  
                                       ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:166:13: note: Left side of '&&' is true  
        if (valid() && !_exited())  
            ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:166:9: note: Taking true branch  
        if (valid() && !_exited())  
        ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:168:13: note: 'code' declared without an initial value  
            int code;  
            ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:169:24: note: Calling 'is_running'  
            auto res = boost::process::detail::api::is_running(_child_handle, code, ec);  
                       ^  
boost/libs/process/include/boost/process/detail/posix/is_running.hpp:47:9: note: Assuming the condition is true  
    if (ret == -1)  
        ^  
boost/libs/process/include/boost/process/detail/posix/is_running.hpp:47:5: note: Taking true branch  
    if (ret == -1)  
    ^  
boost/libs/process/include/boost/process/detail/posix/is_running.hpp:49:13: note: Assuming the condition is false  
        if (errno != ECHILD) //because it no child is running, than this one isn't either, obviously.  
            ^  
/usr/include/x86_64-linux-gnu/bits/errno.h:54:18: note: expanded from macro 'errno'  
#   define errno (*__errno_location ())  
                 ^  
boost/libs/process/include/boost/process/detail/posix/is_running.hpp:49:9: note: Taking false branch  
        if (errno != ECHILD) //because it no child is running, than this one isn't either, obviously.  
        ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:169:24: note: Returning from 'is_running'  
            auto res = boost::process::detail::api::is_running(_child_handle, code, ec);  
                       ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:170:17: note: Left side of '&&' is true  
            if (!res && !_exited())  
                ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:170:13: note: Taking true branch  
            if (!res && !_exited())  
            ^  
boost/libs/process/include/boost/process/detail/child_decl.hpp:171:17: note: 1st function call argument is an uninitialized value  
                _exit_status->store(code);  
                ^  
```

---
