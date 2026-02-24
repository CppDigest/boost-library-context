# #14 - ptr_vector::c_array is not compiling when boost::nullable is used in ptr_vector. [Closed]

> Username: moose-x86  
> Created at: 2017-12-14 09:58:31 UTC  
> Updated at: 2018-04-09 21:32:15 UTC  
> Closed at: 2018-04-09 21:32:15 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/14  

Hi,  
  
using c_array is not possible if boost::ptr_vector<boost::nullable<int>>.  
Compile error is that "can not convert boost::nullable<int>** to int**."  
  
Br,  
Przemek  
  
https://github.com/boostorg/ptr_container/blob/17c3d7ce79ca1756d1ab13c7f315cb90a51cd43f/include/boost/ptr_container/ptr_sequence_adapter.hpp#L539

---

## Comment 1

> Username: eldiener  
> Created at: 2017-12-16 06:15:28 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/14#issuecomment-352164236  

Please show a minimum code example where the compile error occurs.
