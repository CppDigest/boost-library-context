# #23 - bimap broken with C++20 GCC 10 due to allocator changes [Closed]

> Username: mgaunard  
> Created at: 2020-05-14 11:03:18 UTC  
> Updated at: 2020-05-21 15:19:16 UTC  
> Closed at: 2020-05-21 15:19:16 UTC  
> Url: https://github.com/boostorg/bimap/issues/23  

A number of things have been removed from std::allocator, which breaks bimap.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-05-21 15:19:16 UTC  
> Url: https://github.com/boostorg/bimap/issues/23#issuecomment-632145047  

Addressed in 83f4f27f3a3744bb27687362dc3ca73453a66090 and 6fba6e5de3a5d9b0675a875fdac8aad9238d6dd8 .
