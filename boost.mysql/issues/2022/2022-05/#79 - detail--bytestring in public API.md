# #79 - detail::bytestring in public API [Closed]

> Username: vinniefalco  
> Created at: 2022-05-14 15:11:26 UTC  
> Updated at: 2023-02-11 12:02:42 UTC  
> Closed at: 2023-02-11 12:02:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/79  

Public interfaces should not return private types:  
https://anarthal.github.io/mysql/mysql/ref/boost__mysql__row/buffer.html  
  
Perhaps this could return `asio::const_buffer`?

---

## Comment 1

> Username: anarthal  
> Created at: 2022-05-15 16:33:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/79#issuecomment-1126974933  

We should make this function private. It's not to be used by the user.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-15 16:43:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/79#issuecomment-1126976386  

Even better :)
