# #232 - building on 32-bit arm7 warning: cast increases alignment [Closed]

> Username: larsonmpdx  
> Created at: 2017-01-20 21:12:17 UTC  
> Updated at: 2017-02-02 12:29:32 UTC  
> Closed at: 2017-02-01 23:46:33 UTC  
> Url: https://github.com/boostorg/beast/issues/232  

this is with gcc 4.9 on 32 bit armv7.  There are maybe 20-30 of these errors total, all basically the same.  I can study a little more if necessary.  When I run a basic sync server it runs correctly.  I think clang 3.5 gave a similar warning when I tried that.  
  
```  
./deps/Beast/include/beast/core/detail/buffer_cat.hpp:86:36:  
warning: cast from ‘std::array<unsigned char, 4u>::pointer {aka unsigned char*}’  
to ‘const boost::asio::const_buffer**’ increases required alignment of target type [-Wcast-align]  
             iter_t<I>*>(buf_.data());  
                                    ^  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-20 21:13:17 UTC  
> Url: https://github.com/boostorg/beast/issues/232#issuecomment-274181335  

Thanks for reporting this. Are all the warnings in buffer_cat.hpp?

---

## Comment 2

> Username: larsonmpdx  
> Created at: 2017-01-20 21:23:29 UTC  
> Url: https://github.com/boostorg/beast/issues/232#issuecomment-274183465  

all either buffer_cat.hpp line 86 or line 94, or this from basic_streambuf.hpp:  
  
```  
./deps/Beast/include/beast/core/impl/basic_streambuf.ipp:603:40:  
warning: cast from ‘std::allocator_traits<std::allocator<unsigned char> >::pointer {aka unsigned char*}’  
to ‘beast::basic_streambuf<std::allocator<char> >::element*’ increases required alignment of target type [-Wcast-align]  
                 sizeof(element) + size));  
                                        ^  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-20 21:37:19 UTC  
> Updated at: 2017-01-20 21:40:30 UTC  
> Url: https://github.com/boostorg/beast/issues/232#issuecomment-274186467  

This warning is a false positive, there's no bug. Try casting to `void*` first, or try changing the `reinterpret_cast` to a C-style cast:  
  
Cast to `void*` first:  
```  
return *reinterpret_cast<iter_t<I>*>(  
    static_cast<void*>(buf_.data()));  
...  
return *reinterpret_cast<iter_t<I> const*>(  
    static_cast<void*>(buf_.data()));  
...  
auto& e = *reinterpret_cast<element*>(  
    static_cast<void*>(alloc_traits::allocate(  
        this->member(), sizeof(element) + size)));  
```  
  
Use C-style cast:  
```  
return *(iter_t<I>*)(buf_.data());  
...  
return *(iter_t<I> const*)(buf_.data());  
...  
auto& e = *(element*)(alloc_traits::allocate(  
    this->member(), sizeof(element) + size));  
```

---

## Comment 4

> Username: larsonmpdx  
> Created at: 2017-01-20 21:38:58 UTC  
> Url: https://github.com/boostorg/beast/issues/232#issuecomment-274186813  

ok, I'll give that a shot, thanks

---

## Comment 5

> Username: larsonmpdx  
> Created at: 2017-01-22 02:19:38 UTC  
> Url: https://github.com/boostorg/beast/issues/232#issuecomment-274302811  

I got the same warnings with the c-style casts.  adding (void*) or (const void*) in made warnings go away. tests still pass.  I can put in a PR if you want, here are the changed lines:  
  
```  
buffer_cat:86  
        return *reinterpret_cast<  
            iter_t<I>*>((void*)buf_.data());  
buffer_cat:94  
        return *reinterpret_cast<  
            iter_t<I> const*>((const void*)buf_.data());  
basic_streambuf:603  
        auto& e = *reinterpret_cast<element*>(  
            (void*)alloc_traits::allocate(this->member(),  
                sizeof(element) + size));  
```

---

## Comment 6

> Username: larsonmpdx  
> Created at: 2017-02-02 03:51:08 UTC  
> Url: https://github.com/boostorg/beast/issues/232#issuecomment-276861165  

I can confirm this is fixed.  Thanks for this great library, it's perfect as an embedded monitoring server for my application and was the best thing I found in a recent survey of http libs.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-02-02 12:29:32 UTC  
> Url: https://github.com/boostorg/beast/issues/232#issuecomment-276944994  

Glad to hear it, thanks!
