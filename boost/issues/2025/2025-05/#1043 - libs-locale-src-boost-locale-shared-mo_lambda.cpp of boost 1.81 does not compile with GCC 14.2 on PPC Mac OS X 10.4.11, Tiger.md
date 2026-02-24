# #1043 - libs/locale/src/boost/locale/shared/mo_lambda.cpp of boost 1.81 does not compile with GCC 14.2 on PPC Mac OS X 10.4.11, Tiger [Open]

> Username: ballapete  
> Created at: 2025-05-28 16:42:54 UTC  
> Updated at: 2025-05-28 16:42:54 UTC  
> Url: https://github.com/boostorg/boost/issues/1043  

Hello!  
  
I do not understand C++, so my report might be a bit too long. The MacPorts package manager is used to build the software:  
  
```  
    "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D__DARWIN_UNIX03=1 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_LOCALE_DYN_LINK=1 -DBOOST_LOCALE_NO_WINAPI_BACKEND=1 -DBOOST_LOCALE_WITH_ICONV=1 -DBOOST_LOCALE_WITH_ICU=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_NO_LIB=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG  -I"." -I"/opt/local/include" -I"/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src"  -c -o "bin.v2/libs/locale/build/darwin-14.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/shared/mo_lambda.o" "/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp"  
  
In file included from /usr/include/_wctype.h:78,  
                 from /usr/include/wchar.h:115,  
                 from /opt/local/include/gcc14/c++/cwchar:44,  
                 from /opt/local/include/gcc14/c++/bits/postypes.h:40,  
                 from /opt/local/include/gcc14/c++/iosfwd:42,  
                 from /opt/local/include/gcc14/c++/bits/shared_ptr.h:52,  
                 from /opt/local/include/gcc14/c++/memory:80,  
                 from /opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.hpp:11,  
                 from /opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp:7:  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp:215:18: error: expected identifier before '(' token  
  215 |             bool isdigit(char c) { return '0' <= c && c <= '9'; }  
      |                  ^~~~~~~  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp:215:18: error: expected identifier before numeric constant  
  215 |             bool isdigit(char c) { return '0' <= c && c <= '9'; }  
      |                  ^~~~~~~  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp:215:18: error: expected ',' or '...' before numeric constant  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp: In member function 'bool boost::locale::gnu_gettext::lambda::{anonymous}::tokenizer::__isctype(int (*)(char), int)':  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp:215:50: error: 'c' was not declared in this scope  
  215 |             bool isdigit(char c) { return '0' <= c && c <= '9'; }  
      |                                                  ^  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp: In member function 'void boost::locale::gnu_gettext::lambda::{anonymous}::tokenizer::step()':  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp:249:35: error: invalid conversion from 'char' to 'int (*)(char)' [-fpermissive]  
  249 |                 } else if(isdigit(*ptr)) {  
      |                                   ^  
      |                                   |  
      |                                   char  
/opt/local/var/macports/build/_Volumes_BSD-Linux_MacPorts-Ports_devel_boost181/boost181/work/boost_1_81_0/libs/locale/src/boost/locale/shared/mo_lambda.cpp:215:18: note:   initializing argument 1 of 'bool boost::locale::gnu_gettext::lambda::{anonymous}::tokenizer::__isctype(int (*)(char), int)'  
  215 |             bool isdigit(char c) { return '0' <= c && c <= '9'; }  
      |                  ^~~~~~~  
...failed darwin.compile.c++ bin.v2/libs/locale/build/darwin-14.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/shared/mo_lambda.o...  
common.mkdir bin.v2/libs/locale/build/darwin-14.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/util  
  
        mkdir -p "bin.v2/libs/locale/build/darwin-14.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/util"  
      
darwin.compile.c++ bin.v2/libs/locale/build/darwin-14.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/util/codecvt_converter.o  
```  
  
Quite a bit confusing is that compilation continues.  
  
  
--  
  
Pete
