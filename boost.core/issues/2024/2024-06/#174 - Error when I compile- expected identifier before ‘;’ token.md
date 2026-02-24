# #174 - Error when I compile: expected identifier before ‘;’ token [Closed]

> Username: zh-pan  
> Created at: 2024-06-10 18:45:24 UTC  
> Updated at: 2024-06-10 19:06:10 UTC  
> Closed at: 2024-06-10 19:06:10 UTC  
> Url: https://github.com/boostorg/core/issues/174  

![image](https://github.com/boostorg/core/assets/45628282/868762d6-f5fd-4977-a759-a2d98644070f)  
  
  
/usr/include/boost/core/noinit_adaptor.hpp: In member function ‘void boost::noinit_adaptor<A>::destroy(U*)’:  
/usr/include/boost/core/noinit_adaptor.hpp:58:16: error: expected identifier before ‘;’ token  
  
My relate [issue](https://github.com/aliyun/alibabacloud-cpp-sdk/issues/14).  
  
And I find somebody had same error: [the link](https://www.reddit.com/r/cpp_questions/comments/lylqev/getting_error_c7510_with_boost_and_im_not_able_to/).  
  
The issue #81 has relate?  
  
Somebody can help me? Thanks!

---

## Comment 1

> Username: pdimov  
> Created at: 2024-06-10 18:50:58 UTC  
> Url: https://github.com/boostorg/core/issues/174#issuecomment-2159071639  

As explained in the Reddit comments and in issue #81, the error is caused by cpprestsdk defining a macro `U(x)`.  
  
You can avoid it by defining `_TURN_OFF_PLATFORM_STRING` before including the cpprestsdk headers.

---

## Comment 2

> Username: zh-pan  
> Created at: 2024-06-10 19:05:46 UTC  
> Url: https://github.com/boostorg/core/issues/174#issuecomment-2159099695  

> As explained in the Reddit comments and in issue #81, the error is caused by cpprestsdk defining a macro `U(x)`.  
>   
> You can avoid it by defining `_TURN_OFF_PLATFORM_STRING` before including the cpprestsdk headers.  
  
It work！  
  
Thanks again！
