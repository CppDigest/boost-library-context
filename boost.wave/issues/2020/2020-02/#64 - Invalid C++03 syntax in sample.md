# #64 - Invalid C++03 syntax in sample [Closed]

> Username: jefftrull  
> Created at: 2020-02-10 20:02:25 UTC  
> Updated at: 2020-02-10 22:57:08 UTC  
> Closed at: 2020-02-10 22:57:08 UTC  
> Url: https://github.com/boostorg/wave/issues/64  

A recent change introduced the following line into `real_position_token.hpp`:  
  
`using boost::wave::token_id::T_UNKNOWN;`  
  
This is C++11 syntax and will break CI if samples are built there.
