# #989 - SSL_set_tlsext_host_name ? [Closed]

> Username: VikingExplorer  
> Created at: 2018-01-18 19:07:51 UTC  
> Updated at: 2018-01-20 00:28:02 UTC  
> Closed at: 2018-01-19 13:09:10 UTC  
> Url: https://github.com/boostorg/beast/issues/989  

I'm trying to get a client sample to work.  Where does SSL_set_tlsext_host_name come from?

---

## Comment 1

> Username: djarek  
> Created at: 2018-01-18 19:12:14 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358750805  

@VikingExplorer OpenSSL  
https://www.openssl.org/docs/manmaster/man3/SSL_get_servername_type.html

---

## Comment 2

> Username: VikingExplorer  
> Created at: 2018-01-18 20:01:43 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358764723  

ahh, dependency on openssl.  No problem.   I guess vcpkg didn't auto get that.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-18 20:10:10 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358766972  

The dependency is optional, if you don't have OpenSSL then the make scripts will skip it. I didn't want to force people to have it installed (or worse, force the OpenSSL headers to be included for people that don't need it).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-18 20:10:44 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358767135  

Come to think of it, the vcpkg package should probably include OpenSSL since most people will want it and it is already nicely packaged.

---

## Comment 5

> Username: VikingExplorer  
> Created at: 2018-01-18 20:13:16 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358767803  

Makes sense.  I really appreciate the quick response.  Forced to replace ccprestsdk.  Chose beast.    
  
Congrats.  Separation of concerns is awesome.

---

## Comment 6

> Username: VikingExplorer  
> Created at: 2018-01-18 22:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358808908  

I read https://github.com/boostorg/beast/issues/536  
but I'm still confused. The examples didn't seem to compile, so trying this:   
```  
      beast::http::request<beast::http::empty_body> req;  
      req.method( beast::http::verb::get );  
      std::string_view svt( "/" );  
      req.target( svt );  
```  
  
result:  
  
1>HttpWeb.cpp(63): error C2664: 'void boost::beast::http::header<true,Fields>::target(boost::beast::string_view)': cannot convert argument 1 from 'std::string_view' to 'boost::beast::string_view'  
  
Why wouldn't std::string_view work?

---

## Comment 7

> Username: VikingExplorer  
> Created at: 2018-01-18 22:58:43 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358810576  

In string_view.hpp, BOOST_ASIO_HAS_STD_EXPERIMENTAL_STRING_VIEW is undefined, like I think it should be.

---

## Comment 8

> Username: VikingExplorer  
> Created at: 2018-01-18 23:02:37 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358811592  

Changing it to       string_view svt( "/" );  
makes it compile.  
However, now I get lots of link errors:  
1>HttpWeb.obj : error LNK2019: unresolved external symbol _CRYPTO_cleanup_all_ex_data referenced in function "public: __thiscall boost::asio::ssl::detail::openssl_init_base::do_init::~do_init(void)" (??1do_init@openssl_init_base@detail@ssl@asio@boost@@QAE@XZ)  
  
Need to figure out why auto_link.hpp isn't working right.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-01-19 00:20:27 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358826453  

`_CRYPTO_cleanup_all_ex_data` is an OpenSSL function I believe

---

## Comment 10

> Username: VikingExplorer  
> Created at: 2018-01-19 12:56:11 UTC  
> Updated at: 2018-01-19 12:58:21 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358959179  

ahh, fixed link errors with:  
  
#pragma comment(lib, "libeay32.lib")  
#pragma comment(lib, "ssleay32.lib")  
  
back to string_view.  I really need to use a std:string_view or even better, a std::string.  Getting:  
  
1>HttpWeb.cpp(67): error C2664: 'void boost::beast::http::header<true,Fields>::target(boost::beast::string_view)': cannot convert argument 1 from 'std::string_view' to 'boost::beast::string_view'  
  
from request.target, while request.set does accept std::string.    
  
I'm not understanding why boost::beast::string_view even exists.

---

## Comment 11

> Username: VikingExplorer  
> Created at: 2018-01-19 13:09:10 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358961856  

Forced to do this with 3 lines:  
  
      std::string ss( Target.narrow_view() );  
      string_view svt( ss );  
      req.target( svt );

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-01-19 13:36:28 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358967737  

>I'm not understanding why boost::beast::string_view even exists.  
  
It is simply an alias for `boost::string_view`. What you really want to know is why does beast use `boost::string_view` instead of `std::string_view`. It is because beast only requires C++11, and `std::string_view` is C++17.

---

## Comment 13

> Username: VikingExplorer  
> Created at: 2018-01-19 13:37:51 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-358968067  

Ok, makes sense.

---

## Comment 14

> Username: ras0219-msft  
> Created at: 2018-01-20 00:27:14 UTC  
> Updated at: 2018-01-20 00:28:02 UTC  
> Url: https://github.com/boostorg/beast/issues/989#issuecomment-359126604  

I've added `openssl` as a dependency for `boost-asio`, which will make it also automatically installed for `boost-beast`. Thanks for the suggestion!
