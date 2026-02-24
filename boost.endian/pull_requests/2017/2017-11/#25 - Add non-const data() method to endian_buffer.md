# #25 Add non-const data() method to endian_buffer [Closed]

> Username: cy20lin  
> Created at: 2017-11-20 17:06:47 UTC  
> Updated at: 2019-10-16 01:38:50 UTC  
> Closed at: 2019-10-16 01:38:49 UTC  
> Url: https://github.com/boostorg/endian/pull/25  

Allow users directly read data into endian_buffer through non-const data() method.  
  
As described in the docs, endian_buffer is designed to be memcpyable. It is quite reasonable for users to pretend that the buffer point by non-const endian_buffer data() method would also be non-const. And may thus come up with following attempts, trying to access endian_buffer.  
  
```c++  
    boost::endian::big_int32_buf_t i(123);  
    boost::endian::big_int32_buf_t j(456);  
  
    // works  
    std::cout.write(i.data(), sizeof(i));  
  
    // doesn't work, since istream::read requires i.data() to be a pointer to non-const char  
    std::cin.read(i.data(), sizeof(i));  
  
    // works as describe in docs  
    std::memcpy(&i, &j, sizeof(i));  
  
    // doesn't work, same reason above  
    std::memcpy(i.data(), j.data(), sizeof(i));  
```  
  
It feels like there are some inconsistencies when it comes to accessing endian_buffer.  
  
This is just my humble opinion though. I think it would be better to add non-const data() method into endian_buffer.  
  
Or maybe there are some further design decisions that I don't take into considerations?

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-03-12 20:05:22 UTC  
> Url: https://github.com/boostorg/endian/pull/25#issuecomment-472160090  

This seems to me as a reasonable feature. @pdimov can you please trigger the CI?

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-04-28 15:15:50 UTC  
> Url: https://github.com/boostorg/endian/pull/25#issuecomment-487388395  

It is a reasonable feature.  
  
Tangentially related, I don't like the `char const*` return type of `data()` though. Would much rather return `unsigned char const*`, but I'm not sure how much code this is going to break, so I'm soliciting opinions.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-04-29 20:52:02 UTC  
> Url: https://github.com/boostorg/endian/pull/25#issuecomment-487739458  

> Tangentially related, I don't like the char const* return type of data() though. Would much rather return unsigned char const*, but I'm not sure how much code this is going to break, so I'm soliciting opinions.  
  
I also was surprised by the fact that it returns `char` type when I first saw it. From my point of view changing it to `unsigned char` should not be a big problem, because no one must rely on `char` type representation and in all valid uses there already should be a `reinterpret_cast` in user code that either casts destination to `char const*` or a return value of `data()` to `unsigned char const*`. In the first case there need a little effort to make it work and to establish portability, in the second case no actions required form the user.

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-10-16 01:38:49 UTC  
> Url: https://github.com/boostorg/endian/pull/25#issuecomment-542472612  

I've added the non-const `data` overloads and made `data` return `unsigned char*` (on develop.)

---
