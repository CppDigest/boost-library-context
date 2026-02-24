# #381 - Header guard doesn't follow BOOST_ convention [Closed]

> Username: rconde01  
> Created at: 2021-06-29 13:44:57 UTC  
> Updated at: 2021-07-04 16:21:44 UTC  
> Closed at: 2021-07-04 16:21:44 UTC  
> Url: https://github.com/boostorg/config/issues/381  

https://github.com/boostorg/config/blob/3fa057a8c33b81e6f0e7d1951b97389a2dffc2a5/include/boost/detail/workaround.hpp#L5  
  
It is useful to have a consistent pattern of BOOST_XXXX for header guards so they can be changed easily for cases where you want to use an independent copy of boost.
