# #487 - Cannot include a path in Windows shared folder, whose name is started with two slashes [Open]

> Username: yuxianch  
> Created at: 2019-09-28 14:34:26 UTC  
> Updated at: 2021-05-29 18:22:45 UTC  
> Url: https://github.com/boostorg/build/issues/487  

I am editting beast/test/Jamfile to include a path in Windows network shared folder. The format of the path is special, which starts with two slashes. After executing bjam, I only got one backslash in the compilation command, which caused files not found. The path looks like this:  
> //something.xxx.com/dir  
  
And bjam generated "-I\something.xxx.com\dir" in the command, which is not what I want. This is my code:  
```  
import os ;  
local REF_ROOT = [ os.environ SHARED_PATH_DEF ] ;  #get the shared folder path  
local OPENSSL_ROOT = $(REF_ROOT)/OpenSSL/v1.1.1c ;  
path-constant OPENSSL_INC32 : $(OPENSSL_ROOT)/Win32/include ;  
path-constant OPENSSL_LIB32 : $(OPENSSL_ROOT)/Win32/lib ;  
lib lib-test  
    : lib_test.cpp  
    : requirements  
    <link>static  
    $(defines)  
    : usage-requirements  
    <include>./extras/include  
    $(defines)  
    ;  
project /boost/beast/test  
    : requirements  
    [ requires  
        cxx11_constexpr  
        cxx11_decltype  
        cxx11_hdr_tuple  
        #cxx11_sfinae_expr # Every MSVC fails this  
        cxx11_template_aliases  
        cxx11_variadic_templates  
     ]  
    <include>./extern  
    <define>BOOST_BEAST_ALLOW_DEPRECATED  
    <define>BOOST_BEAST_TESTS  
    <library>/boost/beast//lib-asio/<link>static  
    <library>/boost/beast//lib-beast/<link>static  
    <library>/boost/filesystem//boost_filesystem  
    <library>/boost/coroutine//boost_coroutine  
    <include>$(OPENSSL_INC32) <library-path>$(OPENSSL_LIB32)  
    ;  
```  
Does bjam support including path with two continuous slashes? Could somebody help me out? Thanks a lot!!!

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:15 UTC  
> Url: https://github.com/boostorg/build/issues/487#issuecomment-850877374  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
