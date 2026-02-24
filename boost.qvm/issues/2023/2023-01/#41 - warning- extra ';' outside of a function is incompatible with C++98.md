# #41 - warning: extra ';' outside of a function is incompatible with C++98 [Closed]

> Username: jhasse  
> Created at: 2023-01-17 17:50:17 UTC  
> Updated at: 2023-01-21 06:18:01 UTC  
> Closed at: 2023-01-21 06:18:00 UTC  
> Url: https://github.com/boostorg/qvm/issues/41  

When compiling with AppleClang 14 I'm getting the following warnings:  
```  
boost/qvm/vec_traits_gnuc.hpp:88:33: warning: extra ';' outside of a function is incompatible with C++98 [-Wc++98-compat-extra-semi]  
BOOST_QVM_GNUC_VEC_TYPE(float,2);  
                                ^  
boost/qvm/vec_traits_gnuc.hpp:89:33: warning: extra ';' outside of a function is incompatible with C++98 [-Wc++98-compat-extra-semi]  
BOOST_QVM_GNUC_VEC_TYPE(float,4);  
                                ^  
boost/qvm/vec_traits_gnuc.hpp:90:34: warning: extra ';' outside of a function is incompatible with C++98 [-Wc++98-compat-extra-semi]  
BOOST_QVM_GNUC_VEC_TYPE(double,2);  
                                 ^  
boost/qvm/vec_traits_gnuc.hpp:91:34: warning: extra ';' outside of a function is incompatible with C++98 [-Wc++98-compat-extra-semi]  
BOOST_QVM_GNUC_VEC_TYPE(double,4);  
                                 ^  
```  
I could disable the warning, but I guess the semicolon is unnecessary?

---

## Comment 1

> Username: zajo  
> Created at: 2023-01-21 06:18:00 UTC  
> Url: https://github.com/boostorg/qvm/issues/41#issuecomment-1399190374  

Thanks, fixed.
