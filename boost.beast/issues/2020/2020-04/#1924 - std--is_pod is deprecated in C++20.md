# #1924 - std::is_pod is deprecated in C++20 [Closed]

> Username: martinitus  
> Created at: 2020-04-29 15:19:38 UTC  
> Updated at: 2020-05-01 05:16:32 UTC  
> Closed at: 2020-05-01 05:16:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1924  

Hi folks,  
  
I just tried compiling some examples with gcc-10 in c++20 mode. One issue i found was:  
  
https://github.com/boostorg/beast/blob/83fc0018a89863c6c2aa3cf932313664f8651e9b/include/boost/beast/http/span_body.hpp#L39  
  
Which issued warning beacause `std::is_pod` got deprecated in C++20.   
Seems like `span_body.hpp` is the only place where `is_pod` is used.   
  
Potential fixes:  
 - remove static assert  
 - replace with `std::is_standard_layout<T>::value && std::is_trivial<T>::value`   
 - replace with either `std::is_standard_layout<T>::value` or `std::is_trivial<T>::value`    
   depending on what the actual requirements on T are (I don't know o.0)  
  
  
Keep up the nice work :+1:   
Hth,  
Martin

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-29 17:14:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1924#issuecomment-621346674  

I'm going to have to figure out how to install gcc-10 on Fedora-31

---

## Comment 2

> Username: martinitus  
> Created at: 2020-04-30 08:58:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1924#issuecomment-621706750  

:+1:
