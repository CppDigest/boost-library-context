# #286 - base64_alphabet should use constexpr [Closed]

> Username: vinniefalco  
> Created at: 2017-03-26 23:55:35 UTC  
> Updated at: 2017-05-09 14:19:13 UTC  
> Closed at: 2017-05-09 14:19:13 UTC  
> Url: https://github.com/boostorg/beast/issues/286  

`base64_alphabet` is pretty ugly, it uses `std::string` instead of `char constexpr[]`. And the implementation of `base64_decode` calls `std::string::find`. It should use a 256-element `constexpr` lookup table instead.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-09 14:19:13 UTC  
> Url: https://github.com/boostorg/beast/issues/286#issuecomment-300179675  

Fixed in **b39**
