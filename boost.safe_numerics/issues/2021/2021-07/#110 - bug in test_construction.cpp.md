# #110 - bug in test_construction.cpp [Open]

> Username: GregKon  
> Created at: 2021-07-05 14:40:59 UTC  
> Updated at: 2021-07-05 14:40:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/110  

Hi Robert  
  
There is small issue what cause compilation error on non gcc/msvc compilers  
  
in test_construction.cpp line 156 you create   
`using test_types = mp_unique<`  
and include test_values.hpp where in line 66 also create the same alias name:  
`using test_types = boost::mp11::mp_list<`  
  
which alias shall be used?
