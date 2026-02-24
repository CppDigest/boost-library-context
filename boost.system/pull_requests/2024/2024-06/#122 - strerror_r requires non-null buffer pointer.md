# #122 strerror_r requires non-null buffer pointer [Merged]

> Username: ashtum  
> Created at: 2024-06-02 12:20:40 UTC  
> Updated at: 2024-06-03 01:21:57 UTC  
> Merged at: 2024-06-03 01:21:56 UTC  
> Closed at: 2024-06-03 01:21:56 UTC  
> Url: https://github.com/boostorg/system/pull/122  

According to the documentation, in the non-allocating overload of `message`, we can pass a `nullptr` for `buffer` when `len` is 0.  
  
> If len is 0, nothing is copied, but the function still returns buffer. Note that when len is 0, buffer may be nullptr  
  
  
Compiling the following generates `-Wnonnull` warnings, because `buffer` parameter in `strerror_r` signature is marked as non-null:  
https://godbolt.org/z/sdbvfY7vo  
```C++  
#include <boost/system.hpp>  
  
int main()  
{  
  boost::system::error_code ec;  
  ec.message(nullptr, 0);  
}  
```  
  
```  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/system/detail/generic_category.hpp:14,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/system/detail/error_condition.hpp:14,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/system/detail/error_code.hpp:15,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/system/error_code.hpp:13,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/system.hpp:10,  
                 from <source>:1:  
In function 'const char* boost::system::detail::generic_error_category_message(int, char*, std::size_t)',  
    inlined from 'const char* boost::system::detail::system_error_category_message(int, char*, std::size_t)' at /opt/compiler-explorer/libs/boost_1_84_0/boost/system/detail/system_category_message.hpp:62:42,  
    inlined from 'const char* boost::system::error_code::message(char*, std::size_t) const' at /opt/compiler-explorer/libs/boost_1_84_0/boost/system/detail/error_code.hpp:311:57,  
    inlined from 'int main()' at <source>:6:13:  
/opt/compiler-explorer/libs/boost_1_84_0/boost/system/detail/generic_category_message.hpp:42:41: warning: argument 2 null where non-null expected [-Wnonnull]  
   42 |     return strerror_r_helper( strerror_r( ev, buffer, len ), buffer );  
      |                               ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~  
In file included from /opt/compiler-explorer/gcc-13.3.0/include/c++/13.3.0/cstring:42,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/system/detail/generic_category_message.hpp:15:  
/usr/include/string.h: In function 'int main()':  
/usr/include/string.h:444:14: note: in a call to function 'char* strerror_r(int, char*, size_t)' declared 'nonnull'  
  444 | extern char *strerror_r (int __errnum, char *__buf, size_t __buflen)  
      |              ^~~~~~~~~~  
Compiler returned: 0  
```

---

## Comment 1

> Username: ashtum  
> Created_at: 2024-06-02 15:34:33 UTC  
> Url: https://github.com/boostorg/system/pull/122#issuecomment-2143902320  

https://github.com/boostorg/system/actions/runs/9338190106/job/25703151871?pr=122#step:4:3  
```  
  sudo apt-get update  
  sudo apt-get -y install g++-11  
  shell: /usr/bin/bash --noprofile --norc -e -o pipefail {0}  
  env:  
    UBSAN_OPTIONS: print_stacktrace=1  
Get:1 file:/etc/apt/apt-mirrors.txt Mirrorlist [142 B]  
Hit:2 http://azure.archive.ubuntu.com/ubuntu focal InRelease  
Hit:6 https://packages.microsoft.com/repos/azure-cli focal InRelease  
Get:3 http://azure.archive.ubuntu.com/ubuntu focal-updates InRelease [128 kB]  
Get:4 http://azure.archive.ubuntu.com/ubuntu focal-backports InRelease [128 kB]  
Get:5 http://azure.archive.ubuntu.com/ubuntu focal-security InRelease [128 kB]  
Get:7 https://packages.microsoft.com/ubuntu/20.04/prod focal InRelease [3632 B]  
Get:8 http://azure.archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [3332 kB]  
Get:9 http://azure.archive.ubuntu.com/ubuntu focal-updates/main Translation-en [525 kB]  
Get:10 http://azure.archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [1189 kB]  
Get:11 http://azure.archive.ubuntu.com/ubuntu focal-updates/universe Translation-en [285 kB]  
Get:12 http://azure.archive.ubuntu.com/ubuntu focal-security/main amd64 Packages [2956 kB]  
Get:13 http://azure.archive.ubuntu.com/ubuntu focal-security/main Translation-en [444 kB]  
Get:14 http://azure.archive.ubuntu.com/ubuntu focal-security/universe amd64 Packages [963 kB]  
Get:15 http://azure.archive.ubuntu.com/ubuntu focal-security/universe Translation-en [203 kB]  
Get:16 https://packages.microsoft.com/ubuntu/20.04/prod focal/main arm64 Packages [60.6 kB]  
Get:17 https://packages.microsoft.com/ubuntu/20.04/prod focal/main amd64 Packages [293 kB]  
Fetched 10.6 MB in 1s (7192 kB/s)  
Reading package lists...  
Reading package lists...  
Building dependency tree...  
Reading state information...  
clang-11 is already the newest version (1:11.0.0-2~ubuntu20.04.1).  
The following additional packages will be installed:  
  fonts-mathjax libjs-mathjax  
Suggested packages:  
  fonts-mathjax-extras fonts-stix libjs-mathjax-doc  
The following NEW packages will be installed:  
  clang-11-doc clang-11-examples fonts-mathjax libclang-11-dev libjs-mathjax  
  python3-clang-11  
```  
This doesn't make any sense, why it tries to install clang-11!

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-06-02 15:44:59 UTC  
> Url: https://github.com/boostorg/system/pull/122#issuecomment-2143907267  

I'll take care of it.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-06-02 18:11:49 UTC  
> Url: https://github.com/boostorg/system/pull/122#issuecomment-2143974742  

Can you please make the following changes:  
  
* rebase on develop to pick up the new ci.yml  
* remove the changes to generic_category_test and system_category_test  
* remove the inclusion of core/ignore_unused.hpp and the uses of boost::ignore_unused  
  
Thanks.

---
