# #514 Cmake fix v61 [Closed]

> Username: ksergey  
> Created at: 2017-06-19 04:35:27 UTC  
> Updated at: 2017-06-22 05:08:17 UTC  
> Closed at: 2017-06-22 05:08:09 UTC  
> Url: https://github.com/boostorg/beast/pull/514  

Changes for v61 branch #511

---

## Comment 1

> Username: xsacha  
> Created_at: 2017-06-19 22:43:06 UTC  
> Updated_at: 2017-06-19 22:45:47 UTC  
> Url: https://github.com/boostorg/beast/pull/514#issuecomment-309593990  

@ksergey I thought OpenSSL isn't related to Beast. Why is it added to target if found? If I use this as a submodule, would I want the target to have OpenSSL? If so, maybe that should be optional too.  
  
Btw, thanks for the options rebased on v61!

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-06-19 22:49:18 UTC  
> Url: https://github.com/boostorg/beast/pull/514#issuecomment-309595085  

Beast does not require OpenSSL but the tests and examples will use it if found. And if you want to use OpenSSL sockets with Beast you will of course have to configure OpenSSL includes and link libraries.

---

## Comment 3

> Username: xsacha  
> Created_at: 2017-06-19 22:51:04 UTC  
> Url: https://github.com/boostorg/beast/pull/514#issuecomment-309595431  

@vinniefalco Will some features of Beast (eg. https) not work without OpenSSL linked? Or is it only for things specific to the example?  
I'm thinking if it is needed for certain features of Beast, it could be an option like Beast_USE_OPENSSL. Otherwise it shouldn't be linked to Beast.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-06-19 23:29:06 UTC  
> Url: https://github.com/boostorg/beast/pull/514#issuecomment-309601477  

Its for the example. If you don't want to use OpenSSL in your own program that uses Beast, you don't have to.

---

## Comment 5

> Username: ksergey  
> Created_at: 2017-06-21 06:00:48 UTC  
> Url: https://github.com/boostorg/beast/pull/514#issuecomment-309971944  

@vinniefalco INTERFACE target provides ability to link Beast with all necessary third party libraries without additional code in parent CMakeLists.txt.  
  
I agree with @xsacha - it will be good to have BEAST_USE_OPENSSL option.  
  
Example1:  
```  
option(BEAST_USE_OPENSSL "" ON)  
add_executable(app main.cpp)  
target_link_libraries(app Beast)  
```  
  
Example2:  
```  
find_package(OpenSSL REQUIRED)  
include_directories(${OPENSSL_INCLUDE_DIR})  
add_executable(app main.cpp)  
target_link_libraries(app Beast ${OPENSSL_LIBRARIES})  
```  
  
For me example 1 looks much better than example 2.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-06-21 06:06:20 UTC  
> Url: https://github.com/boostorg/beast/pull/514#issuecomment-309972802  

I don't think it should be Beast's job to help users find OpenSSL, that responsibility lies with Asio if anything. Also note, I have already starting using `BEAST_USE_OPENSSL` as a macro to conditionally compile tests and examples if OpenSSL is found.

---

## Comment 7

> Username: xsacha  
> Created_at: 2017-06-21 22:25:08 UTC  
> Url: https://github.com/boostorg/beast/pull/514#issuecomment-310222634  

@ksergey,  
Like vinnie said, the actual Beast project isn't dependent on OpenSSL so the target should not require it to be linked (even if Beast is using OpenSSL for some task, it is using it indirectly).  
So this should probably be dropped. I know it means you probably need to link OpenSSL separately but it isn't that big of a deal hopefully :)

---
