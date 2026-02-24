# #539 - Mixing std::uint64_t and std::size_t [Closed]

> Username: octopus-prime  
> Created at: 2017-06-24 18:05:53 UTC  
> Updated at: 2017-06-24 18:33:41 UTC  
> Closed at: 2017-06-24 18:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/539  

Because of #536 i had a deeper look to your body implementations.  
There you mix `std::uint64_t` and `std::size_t`.  
Is this purpose or mistake?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-24 18:19:06 UTC  
> Url: https://github.com/boostorg/beast/issues/539#issuecomment-310855764  

It is on purpose. Beast supports HTTP messages whose Content-Length is up to 2^64-1 bytes, even when the address model used to build the application is 32-bit (i.e. when `std::size_t` is 32 bits). So I have been very careful to use `std::size_t` in places where it makes sense, such as when referring to the size of a dynamic allocation, and to use `std::uint64_t` when it refers to the size of something specified by the protocol such as the content length.

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-06-24 18:33:33 UTC  
> Url: https://github.com/boostorg/beast/issues/539#issuecomment-310857194  

okay -> close
