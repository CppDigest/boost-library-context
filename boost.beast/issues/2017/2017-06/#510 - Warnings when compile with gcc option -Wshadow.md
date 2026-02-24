# #510 - Warnings when compile with gcc option -Wshadow [Closed]

> Username: ksergey  
> Created at: 2017-06-18 18:31:45 UTC  
> Updated at: 2017-06-19 17:51:29 UTC  
> Closed at: 2017-06-19 17:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/510  

Examples:  
```  
/home/ksergey/dev/beast_ws/deps/Beast/include/beast/websocket/impl/stream.ipp:293:36: warning: declaration of ‘accept’ shadows a member of ‘beast::websocket::stream<NextLayer>’ [-Wshadow]                                                                      detail::sec_ws_accept_type accept;  
  
/home/ksergey/dev/beast_ws/deps/Beast/include/beast/zlib/detail/deflate_stream.hpp:2285:23: warning: declaration of ‘init’ shadows a member of ‘beast::zlib::detail::deflate_stream’ [-Wshadow]     
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-18 20:19:24 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309300377  

Thanks! Looking into it...

---

## Comment 2

> Username: ksergey  
> Created at: 2017-06-19 04:24:56 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309335567  

Also check this:  
```  
/home/ksergey/dev/Beast/include/beast/http/impl/serializer.ipp:192:9: warning: this statement may fall through [-Wimplicit-fallthrough=]  
         s_ = do_body_c + 1;  
         ^~  
/home/ksergey/dev/Beast/include/beast/http/impl/serializer.ipp:195:5: note: here  
     case do_body_c + 1:  
     ^~~~  
/home/ksergey/dev/Beast/include/beast/http/impl/serializer.ipp:218:26: warning: this statement may fall through [-Wimplicit-fallthrough=]  
         BOOST_FALLTHROUGH;  
                          ^  
/home/ksergey/dev/Beast/include/beast/http/impl/serializer.ipp:221:5: note: here  
     case do_body_c + 2:  
     ^~~~  
/home/ksergey/dev/Beast/include/beast/http/impl/serializer.ipp:239:9: warning: this statement may fall through [-Wimplicit-fallthrough=]  
         s_ = do_final_c + 1;  
         ^~  
/home/ksergey/dev/Beast/include/beast/http/impl/serializer.ipp:242:5: note: here  
     case do_final_c + 1:  
  
/home/ksergey/dev/Beast/include/beast/zlib/detail/deflate_stream.hpp: In function ‘void beast::zlib::detail::deflate_stream::doWrite(beast::zlib::z_params&, beast::zlib::Flush, beast::error_code&) [with <template-parameter-1-1> = void]’:  
/home/ksergey/dev/Beast/include/beast/zlib/detail/deflate_stream.hpp:1052:30: warning: ‘*((void*)& old_flush +4)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
     else if(zs.avail_in == 0 && flush <= old_flush &&  
  
```  
It's v61

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-19 04:39:37 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309336955  

Well since the very next lines there say `BOOST_FALLTHROUGH;` you might think it was intended :)  
  
Nothing I can really do about that though

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-19 04:42:16 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309337217  

As for `‘*((void*)& old_flush +4)’ may be used uninitialized in this function` that is almost certainly a false positive, but I have not figured out a way to shuffle the code to silence the warning. Any ideas?

---

## Comment 5

> Username: ksergey  
> Created at: 2017-06-19 04:57:37 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309338674  

https://dzone.com/articles/implicit-fallthrough-in-gcc-7 ??

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-19 05:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309338986  

What are you suggesting exactly?

---

## Comment 7

> Username: ksergey  
> Created at: 2017-06-19 05:07:36 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309339801  

@vinniefalco define macro BEAST_FALLTHROUGHT  
```  
#if defined(__GNUC__) && (__GNUC__ >= 7) && ! defined(__clang__)  
#if __cplusplus <= 199711L // up to C++03  
#define BEAST_FALLTHROUGHT __attribute__ ((fallthrough))  
#else  
#if __cplusplus >= 201703L  
#define BEAST_FALLTHROUGHT [[fallthrough]]  
#else  
#define BEAST_FALLTHROUGHT [[gnu::fallthrough]]  
#endif  
#endif // __cplusplus <= 199711L  
#else  
#define BEAST_FALLTHROUGHT  
#endif // __GNUC__  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-19 05:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309340048  

Any idea how to add gcc-7 to the build matrix? Then I could keep the warnings away:  
https://github.com/vinniefalco/Beast/blob/master/.travis.yml#L86

---

## Comment 9

> Username: ksergey  
> Created at: 2017-06-19 05:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309340257  

Sorry, but I'm not familiar with travis

---

## Comment 10

> Username: ksergey  
> Created at: 2017-06-19 05:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/510#issuecomment-309340418  

Better variant, I think  
```  
#ifdef __has_cpp_attribute  
#  if __has_cpp_attribute(fallthrough) && __cplusplus > 201402L  
#    define BEAST_FALLTHROUGHT [[fallthrough]]  
#  elif __has_cpp_attribute(clang::fallthrough)  
#    define BEAST_FALLTHROUGHT [[clang::fallthrough]]  
#  elif __has_cpp_attribute(gnu::fallthrough)  
#    define BEAST_FALLTHROUGHT [[gnu::fallthrough]]  
#  endif  
#endif  
#ifndef BEAST_FALLTHROUGHT  
#  if __clang__  
#    define BEAST_FALLTHROUGHT [[clang::fallthrough]]  
#  else  
#    define BEAST_FALLTHROUGHT while (0)  
#  endif  
#endif  
```
