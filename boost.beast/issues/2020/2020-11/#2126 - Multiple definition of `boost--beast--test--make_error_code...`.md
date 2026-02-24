# #2126 - Multiple definition of `boost::beast::test::make_error_code...` [Closed]

> Username: dimarusyy  
> Created at: 2020-11-24 16:41:17 UTC  
> Updated at: 2020-11-24 16:55:01 UTC  
> Closed at: 2020-11-24 16:55:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2126  

I was trying to switch to a shared object linkage for libraries that is using boost::beast from vcpkg.   
I do compilation with BOOST_BEAST_SEPARATE_COMPILATION to enable BOOST_BEAST_HEADER_ONLY .   
Additionally, beast/src.hpp is included as base64 encoding/decoding is used from _experimental .  
And that causes linkage error  
>[build] ../vcpkg_installed/x64-linux/include/boost/beast/_experimental/test/impl/error.ipp:55: multiple definition of >boost::beast::test::make_error_code(boost::beast::test::error); ../transport/wss_client.cpp.o:../vcpkg_installed/x64->linux/include/boost/beast/_experimental/test/impl/error.ipp:55: first defined here  
>[build] /usr/bin/ld: ../mls/mls_auth.cpp.o: in function boost::beast::test::fail_count::fail_count(unsigned long, >boost::system::error_code):  
  
and a bunch of others similar messages.  
I can link statically fine but looks like make_error_code should be declared as inline in .ipp-file.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-24 16:43:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2126#issuecomment-733100114  

using beast as a shared library is not supported

---

## Comment 2

> Username: dimarusyy  
> Created at: 2020-11-24 16:55:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2126#issuecomment-733106768  

> using beast as a shared library is not supported  
  
Ok, makes sense, thanks.
