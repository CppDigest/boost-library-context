# #155 - openssl and macro error [Closed]

> Username: wanglei-fenyu  
> Created at: 2023-05-29 01:48:00 UTC  
> Updated at: 2023-06-22 14:32:26 UTC  
> Closed at: 2023-06-22 14:32:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/155  

boost 18,2 Which version of Openssl to use?   BOOST_MPL_PP_DEFAULT_PARAMS  and BOOST_MPL_AUX_NA_SPEC Macro error.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-05-29 09:06:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/155#issuecomment-1566800178  

Hi @wanglei-fenyu,  
  
Both OpenSSL 1.1.1.x and OpenSSL 3.0 should work. Are you having any trouble with any of these?  
  
> BOOST_MPL_PP_DEFAULT_PARAMS and BOOST_MPL_AUX_NA_SPEC Macro error.  
  
I need more information to diagnose this. Please answer the following questions and include any other extra info available:  
* What operating system and compiler are you using?  
* Are you using any other Boost libraries? Which ones?  
* What is the exact text of the compiler error?  
* How are you building your application? Are you using CMake?  
  
Regards,  
Ruben.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-06-02 09:00:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/155#issuecomment-1573397883  

Hi @wanglei-fenyu, any news on this?

---

## Comment 3

> Username: anarthal  
> Created at: 2023-06-22 14:32:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/155#issuecomment-1602748119  

Closing due to inactivity.
