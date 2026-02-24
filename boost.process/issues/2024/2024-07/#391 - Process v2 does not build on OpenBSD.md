# #391 - Process v2 does not build on OpenBSD [Closed]

> Username: brad0  
> Created at: 2024-07-26 21:32:12 UTC  
> Updated at: 2024-10-24 23:01:00 UTC  
> Closed at: 2024-10-24 23:00:59 UTC  
> Url: https://github.com/boostorg/process/issues/391  

Boost 1.86.0 beta1 on OpenBSD/amd64 -current.  
  
```  
common.mkdir bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/posix  
  
        mkdir -p "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/posix"  
  
common.mkdir bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/windows  
  
        mkdir -p "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/windows"  
  
clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/posix/close_handles.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v  
2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/posix/close_handles.o" "libs/process/src/posix/close_handles.cpp"  
  
libs/process/src/posix/close_handles.cpp:125:15: error: no member named 'close_range' in the global namespace  
            ::close_range(0, whitelist.front() - 1, 0);  
            ~~^  
libs/process/src/posix/close_handles.cpp:135:19: error: no member named 'close_range' in the global namespace  
                ::close_range(mine + 1, next - 1, 0);  
                ~~^  
2 errors generated.  
clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/windows/default_launcher.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v  
2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/windows/default_launcher.o" "libs/process/src/windows/default_launcher.cpp"  
  
clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/proc_info.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v  
2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/proc_info.o" "libs/process/src/ext/proc_info.cpp"  
  
clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/cmd.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v  
2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/cmd.o" "libs/process/src/ext/cmd.cpp"  
  
libs/process/src/ext/cmd.cpp:346:61: error: no viable conversion from returned value of type 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to function return type 'shell'  
    if (!kd) {BOOST_PROCESS_V2_ASSIGN_LAST_ERROR(ec) return vec;}  
                                                            ^~~  
./boost/process/v2/shell.hpp:66:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'basic_cstring_ref<char_type>' (aka 'basic_cstring_ref<char>') for 1st argument  
    shell(basic_cstring_ref<char_type> input) : input_(input) {parse_();}  
    ^  
./boost/process/v2/shell.hpp:67:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'basic_string_view<typename std::conditional<std::is_same<char_type, char>::value, wchar_t, char>::type>' (  
aka 'basic_string_view<wchar_t>') for 1st argument  
    shell(basic_string_view<  
    ^  
./boost/process/v2/shell.hpp:75:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'const shell &' for 1st argument  
    shell(const shell &) = delete;  
    ^  
./boost/process/v2/shell.hpp:78:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'shell &&' for 1st argument  
    shell(shell && lhs) noexcept  
    ^  
./boost/process/v2/shell.hpp:60:5: note: candidate template ignored: could not match 'basic_string_view' against 'vector'  
    shell(basic_string_view<Char, Traits> input)  
    ^  
libs/process/src/ext/cmd.cpp:357:5: error: no matching function for call to 'kvm_close'  
    kvm_close(kd);  
    ^~~~~~~~~  
/usr/include/kvm.h:56:7: note: candidate function not viable: no known conversion from 'std::unique_ptr<kvm_t, closer>' (aka 'unique_ptr<__kvm, closer>') to 'kvm_t *' (aka '__kvm *') for 1st argument  
int       kvm_close(kvm_t *);  
          ^  
2 errors generated.  
  
...failed updating 0 target...  
```

---

## Comment 1

> Username: brad0  
> Created at: 2024-08-24 11:46:37 UTC  
> Url: https://github.com/boostorg/process/issues/391#issuecomment-2308365442  

After applying my work in progress PR at https://github.com/boostorg/process/pull/395 there are still some issues I am not sure  how to resolve.  
  
OpenBSD does not have wordexp.h / wordexp().  
  
```  
clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/shell.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/shell.o" "libs/process/src/shell.cpp"  
  
libs/process/src/shell.cpp:23:10: fatal error: 'wordexp.h' file not found  
#include <wordexp.h>  
         ^~~~~~~~~~~  
1 error generated.  
clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/environment.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/environment.o" "libs/process/src/environment.cpp"  
  
clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/cmd.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/cmd.o" "libs/process/src/ext/cmd.cpp"  
  
libs/process/src/ext/cmd.cpp:346:61: error: no viable conversion from returned value of type 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to function return type 'shell'  
    if (!kd) {BOOST_PROCESS_V2_ASSIGN_LAST_ERROR(ec) return vec;}  
                                                            ^~~  
./boost/process/v2/shell.hpp:66:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'basic_cstring_ref<char_type>' (aka 'basic_cstring_ref<char>') for 1st argument  
    shell(basic_cstring_ref<char_type> input) : input_(input) {parse_();}  
    ^  
./boost/process/v2/shell.hpp:67:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'basic_string_view<typename std::conditional<std::is_same<char_type, char>::value, wchar_t, char>::type>' (aka 'basic_string_view<wchar_t>') for 1st argument  
    shell(basic_string_view<  
    ^  
./boost/process/v2/shell.hpp:75:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'const shell &' for 1st argument  
    shell(const shell &) = delete;  
    ^  
./boost/process/v2/shell.hpp:78:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'shell &&' for 1st argument  
    shell(shell && lhs) noexcept  
    ^  
./boost/process/v2/shell.hpp:60:5: note: candidate template ignored: could not match 'basic_string_view' against 'vector'  
    shell(basic_string_view<Char, Traits> input)  
    ^  
libs/process/src/ext/cmd.cpp:357:5: error: no matching function for call to 'kvm_close'  
    kvm_close(kd);  
    ^~~~~~~~~  
/usr/include/kvm.h:56:7: note: candidate function not viable: no known conversion from 'std::unique_ptr<kvm_t, closer>' (aka 'unique_ptr<__kvm, closer>') to 'kvm_t *' (aka '__kvm *') for 1st argument  
int       kvm_close(kvm_t *);  
          ^  
2 errors generated.  
clang-linux.compile.c++ bin.v2/libs/program_options/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/config_file.o  
  
    "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/program_options/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/config_file.o" "libs/program_options/src/config_file.cpp"  
  
  
...failed updating 0 target...  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-08-26 12:03:13 UTC  
> Url: https://github.com/boostorg/process/issues/391#issuecomment-2310040992  

@time-killer-games are you set up to test this stuff?

---

## Comment 3

> Username: ghost  
> Created at: 2024-08-27 07:17:29 UTC  
> Url: https://github.com/boostorg/process/issues/391#issuecomment-2311749372  

> @time-killer-games are you set up to test this stuff?  
  
I have an OpenBSD install handy if that's why you mean. I'll try it out soon.

---

## Comment 4

> Username: ghost  
> Created at: 2024-08-27 07:21:33 UTC  
> Url: https://github.com/boostorg/process/issues/391#issuecomment-2311756166  

> After applying my work in progress PR at https://github.com/boostorg/process/pull/395 there are still some issues I am not sure  how to resolve.  
>   
> OpenBSD does not have wordexp.h / wordexp().  
>   
> ```  
> clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/shell.o  
>   
>     "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/shell.o" "libs/process/src/shell.cpp"  
>   
> libs/process/src/shell.cpp:23:10: fatal error: 'wordexp.h' file not found  
> #include <wordexp.h>  
>          ^~~~~~~~~~~  
> 1 error generated.  
> clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/environment.o  
>   
>     "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/environment.o" "libs/process/src/environment.cpp"  
>   
> clang-linux.compile.c++ bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/cmd.o  
>   
>     "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/ext/cmd.o" "libs/process/src/ext/cmd.cpp"  
>   
> libs/process/src/ext/cmd.cpp:346:61: error: no viable conversion from returned value of type 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to function return type 'shell'  
>     if (!kd) {BOOST_PROCESS_V2_ASSIGN_LAST_ERROR(ec) return vec;}  
>                                                             ^~~  
> ./boost/process/v2/shell.hpp:66:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'basic_cstring_ref<char_type>' (aka 'basic_cstring_ref<char>') for 1st argument  
>     shell(basic_cstring_ref<char_type> input) : input_(input) {parse_();}  
>     ^  
> ./boost/process/v2/shell.hpp:67:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'basic_string_view<typename std::conditional<std::is_same<char_type, char>::value, wchar_t, char>::type>' (aka 'basic_string_view<wchar_t>') for 1st argument  
>     shell(basic_string_view<  
>     ^  
> ./boost/process/v2/shell.hpp:75:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'const shell &' for 1st argument  
>     shell(const shell &) = delete;  
>     ^  
> ./boost/process/v2/shell.hpp:78:5: note: candidate constructor not viable: no known conversion from 'std::vector<std::string>' (aka 'vector<basic_string<char>>') to 'shell &&' for 1st argument  
>     shell(shell && lhs) noexcept  
>     ^  
> ./boost/process/v2/shell.hpp:60:5: note: candidate template ignored: could not match 'basic_string_view' against 'vector'  
>     shell(basic_string_view<Char, Traits> input)  
>     ^  
> libs/process/src/ext/cmd.cpp:357:5: error: no matching function for call to 'kvm_close'  
>     kvm_close(kd);  
>     ^~~~~~~~~  
> /usr/include/kvm.h:56:7: note: candidate function not viable: no known conversion from 'std::unique_ptr<kvm_t, closer>' (aka 'unique_ptr<__kvm, closer>') to 'kvm_t *' (aka '__kvm *') for 1st argument  
> int       kvm_close(kvm_t *);  
>           ^  
> 2 errors generated.  
> clang-linux.compile.c++ bin.v2/libs/program_options/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/config_file.o  
>   
>     "c++"   -fvisibility-inlines-hidden -Wall -fvisibility=hidden -Wno-inline -m64 -O2 -pipe   -O2 -pipe   -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/program_options/build/clang-linux-16/release/x86_64/link-static/pch-off/visibility-hidden/config_file.o" "libs/program_options/src/config_file.cpp"  
>   
>   
> ...failed updating 0 target...  
> ```  
  
OpenBSD developers last I checked don't have wordexp() because they consider it a security risk and they dont intend on ever adding it. I actually looked into that in the past myself because I also needed it for something. The best you can do is omit any code relying on wordexp on OpenBSD in specific while not effecting its use on other platforms. There is no clear workaround sadly.
