# #92 - cpp_int.hpp, unused parameter warning in clang [Closed]

> Username: HDembinski  
> Created at: 2018-10-25 23:31:37 UTC  
> Updated at: 2019-01-24 11:40:25 UTC  
> Closed at: 2019-01-24 11:40:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/92  

cpp_int.hpp:833  
`BOOST_MP_FORCEINLINE void resize(unsigned new_size, unsigned min_size)`  
  
raises a warning on clang compilers, since new_size is not used inside the function. Please change this to  
  
`BOOST_MP_FORCEINLINE void resize(unsigned /* new_size */, unsigned min_size)`

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-24 11:40:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/92#issuecomment-457167350  

Closing in favour of the PR.
