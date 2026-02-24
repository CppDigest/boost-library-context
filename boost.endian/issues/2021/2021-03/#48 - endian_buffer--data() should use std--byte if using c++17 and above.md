# #48 - endian_buffer::data() should use std::byte if using c++17 and above [Open]

> Username: Julien-Blanc-tgcm  
> Created at: 2021-03-04 10:21:06 UTC  
> Updated at: 2021-03-04 10:21:06 UTC  
> Url: https://github.com/boostorg/endian/issues/48  

When using a c++17 compiler, we may take advantage of the strongly typed std::byte class instead of relying on the arithmetic type char, whether signed (prior to 1.72) or not (1.72 and above).  
  
Since that would probably break client code, it could be an opt-in feature.
