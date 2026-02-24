# #549 - About cpp_int::size() and MinBits [Closed]

> Username: afabri  
> Created at: 2023-04-18 09:10:45 UTC  
> Updated at: 2023-04-21 17:40:14 UTC  
> Closed at: 2023-04-21 17:40:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/549  

I use `boost::multiprecision::number<boost::multiprecision::cpp_int_backend<512> >`  and perform multiplications on them.  
Does that mean that there should be up to  512/64 limbs  not allocated dynamically?     
  
I ask because I have only few multiplications where the result has more than 8 limbs (I create a histogram on the value  returned by the helper function `cpp_int::size()`), but in vtune I see that I still spend a lot of time in  the `resize()`  call in [`multiply.h`](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int/multiply.hpp#L449)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-04-18 17:55:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/549#issuecomment-1513580009  

>I use boost::multiprecision::number<boost::multiprecision::cpp_int_backend<512> > and perform multiplications on them.  
Does that mean that there should be up to 512/64 limbs not allocated dynamically?  
  
No.  That type has an internal cache size of 512 bits and *no upper limit*.  
  
Use `cpp_int_backend<512, 512>` if you want a non-resizable type, or better still `boost::multiprecision::int512_t`
