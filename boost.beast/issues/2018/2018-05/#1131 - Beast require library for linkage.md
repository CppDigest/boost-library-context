# #1131 - Beast require library for linkage? [Closed]

> Username: xushijie  
> Created at: 2018-05-16 16:39:59 UTC  
> Updated at: 2018-06-23 02:20:23 UTC  
> Closed at: 2018-06-23 02:20:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1131  

I add beast to ethminer, and want to submit some runtime statistics, in JSON format, to a remote server using HTTP  POST.  But the linkage fails, which is unexpected. I think beast should be header only and it does not require any external library for linkage.   
  
Can you help to have a look. Thanks  
  
The error message is:   
  
  
`[ 97%] Linking CXX executable ethminer  
cd /other/projectbase/ethminer/build/ethminer && /opt/pro/cmake-3.9.4-Linux-x86_64/bin/cmake -E cmake_link_script CMakeFiles/ethminer.dir/link.txt --verbose=1  
/usr/bin/c++   -Wall -Wno-unknown-pragmas -Wextra -Wno-error=parentheses -pedantic -g  -rdynamic CMakeFiles/ethminer.dir/main.cpp.o  -o ethminer ../libethcore/libethcore.a ../libpoolprotocols/libpoolprotocols.a ../libdevcore/libdevcore.a ../libethminer-buildinfo.a ../libapicore/libapicore.a ../libethash-cl/libethash-cl.a ../libethcore/libethcore.a ../libethash-cl/libethash-cl.a ../libhwmon/libhwmon.a ../libethash/libethash-legacy.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libOpenCL.a ../deps/lib/libethash.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libjsonrpccpp-client.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libcurl.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libz.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libssl.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libcrypto.a -ldl /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libnetwork-uri.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libboost_system-mt.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libboost_filesystem-mt.a ../libdevcore/libdevcore.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libboost_system-mt-d.a ../libethminer-buildinfo.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libjsonrpccpp-server.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libjsonrpccpp-common.a /home/shijiex/.hunter/_Base/2f04d1b/e63b79f/1bc9ed8/Install/lib/libjsoncpp.a -lpthread   
../libapicore/libapicore.a(Api.cpp.o): In function `HttpApi::postData(std::string)':  
/other/projectbase/ethminer/libapicore/Api.cpp:73: undefined reference to `boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >::message<int, void>(boost::beast::http::verb, boost::basic_string_view<char, std::char_traits<char> >, int)'  
../libapicore/libapicore.a(Api.cpp.o): In function `boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >::prepare_payload()':  
/other/projectbase/ethminer/libapicore/../beast/include/boost/beast/http/message.hpp:854: undefined reference to `boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >::prepare_payload(std::integral_constant<bool, true>)'  
../libapicore/libapicore.a(Api.cpp.o): In function `boost::beast::http::serializer<false, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::allocator<char> > >, boost::beast::http::basic_fields<std::allocator<char> > >::fwrinit(std::integral_constant<bool, false>)':  
/other/projectbase/ethminer/libapicore/../beast/include/boost/beast/http/impl/serializer.ipp:39: undefined reference to `boost::beast::http::header<false, boost::beast::http::basic_fields<std::allocator<char> > >::result_int() const'  
../libapicore/libapicore.a(Api.cpp.o): In function `boost::beast::http::serializer<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >::fwrinit(std::integral_constant<bool, true>)':  
/other/projectbase/ethminer/libapicore/../beast/include/boost/beast/http/impl/serializer.ipp:30: undefined reference to `boost::beast::http::header<true, boost::beast::http::basic_fields<std::allocator<char> > >::method() const'  
../libapicore/libapicore.a(Api.cpp.o): In function `boost::beast::http::parser<false, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::allocator<char> > >, std::allocator<char> >::on_response_impl(int, boost::basic_string_view<char, std::char_traits<char> >, int, boost::system::error_code&)':  
/other/projectbase/ethminer/libapicore/../beast/include/boost/beast/http/parser.hpp:366: undefined reference to `boost::beast::http::header<false, boost::beast::http::basic_fields<std::allocator<char> > >::result(unsigned int)'  
/other/projectbase/ethminer/libapicore/../beast/include/boost/beast/http/parser.hpp:370: undefined reference to `boost::beast::http::header<false, boost::beast::http::basic_fields<std::allocator<char> > >::reason(boost::basic_string_view<char, std::char_traits<char> >)'  
collect2: error: ld returned 1 exit status  
ethminer/CMakeFiles/ethminer.dir/build.make:123: recipe for target 'ethminer/ethminer' failed  
make[2]: *** [ethminer/ethminer] Error 1  
make[2]: Leaving directory '/other/projectbase/ethminer/build'  
CMakeFiles/Makefile2:653: recipe for target 'ethminer/CMakeFiles/ethminer.dir/all' failed  
make[1]: *** [ethminer/CMakeFiles/ethminer.dir/all] Error 2  
make[1]: Leaving directory '/other/projectbase/ethminer/build'  
`  
  
### Version of Beast  
  
BOOST_BEAST_VERSION 168 in boost/beast/version.hpp  
  
### Steps necessary to reproduce the problem  
  
**https://github.com/xushijie/ethminer/blob/dev/libapicore/Api.cpp**  
  
The method is:   
`bool HttpApi::postData(string json){  
}`  
  
  
### All relevant compiler information  
  
beast/include$ c++ -v  
Using built-in specs.  
COLLECT_GCC=c++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/5/lto-wrapper  
Target: x86_64-linux-gnu  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 5.4.0-6ubuntu1~16.04.5' --with-bugurl=file:///usr/share/doc/gcc-5/README.Bugs --enable-languages=c,ada,c++,java,go,d,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-5 --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --with-system-zlib --disable-browser-plugin --enable-java-awt=gtk --enable-gtk-cairo --with-java-home=/usr/lib/jvm/java-1.5.0-gcj-5-amd64/jre --enable-java-home --with-jvm-root-dir=/usr/lib/jvm/java-1.5.0-gcj-5-amd64 --with-jvm-jar-dir=/usr/lib/jvm-exports/java-1.5.0-gcj-5-amd64 --with-arch-directory=amd64 --with-ecj-jar=/usr/share/java/eclipse-ecj.jar --enable-objc-gc --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
Thread model: posix  
gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.5)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-16 18:26:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1131#issuecomment-389619100  

No idea what is going on there, but you have to link with at least Boost.System.

---

## Comment 2

> Username: xushijie  
> Created at: 2018-05-17 00:41:29 UTC  
> Updated at: 2018-05-17 00:54:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1131#issuecomment-389708718  

@vinniefalco  is it libboost_system-mt.a?  libboost_system-mt.a is already here, as shown in the linking log.  
I tried run `nm  libboost_system-mt.a` and grep the symbols, but failed to find the `boost::beast::http::header` and `boost::beast::http::header`.   
  
I use CMakeList and hunter. Today, i will try a simple example using console compilation command, and then let you know the result. Thanks.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-17 00:51:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1131#issuecomment-389710223  

I'm not sure how you managed that. Do the examples compile for you, using b2?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-06-16 01:39:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1131#issuecomment-397777681  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-06-23 02:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1131#issuecomment-399623403  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
