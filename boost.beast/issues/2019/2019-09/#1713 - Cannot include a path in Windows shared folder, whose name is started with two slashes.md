# #1713 - Cannot include a path in Windows shared folder, whose name is started with two slashes [Closed]

> Username: yuxianch  
> Created at: 2019-09-28 14:23:06 UTC  
> Updated at: 2019-10-29 00:18:33 UTC  
> Closed at: 2019-10-29 00:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1713  

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
  
Does bjam support including path with two slashes? Could somebody help me out? Thanks a lot!!!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-28 14:28:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1713#issuecomment-536194460  

Try opening an issue here -> https://github.com/boostorg/build

---

## Comment 2

> Username: yuxianch  
> Created at: 2019-09-28 14:31:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1713#issuecomment-536194718  

@vinniefalco Okay. Thank you so much!

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-10-28 15:30:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1713#issuecomment-547002314  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: yuxianch  
> Created at: 2019-10-29 00:15:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1713#issuecomment-547201054  

No: (

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-10-29 00:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1713#issuecomment-547201644  

Well... there is nothing actionable for beast here so I'll go ahead and close it.
