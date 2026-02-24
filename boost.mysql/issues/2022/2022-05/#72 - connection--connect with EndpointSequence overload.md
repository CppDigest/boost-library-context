# #72 - connection::connect with EndpointSequence overload [Closed]

> Username: anarthal  
> Created at: 2022-05-12 17:24:48 UTC  
> Updated at: 2024-08-10 16:09:05 UTC  
> Closed at: 2024-08-10 16:09:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/72  

Allow calling `connection::connect` with an argument satisfying the `EndpointSequence` concept, as returned by `basic_resolver::resolve`.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:09:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/72#issuecomment-2282196646  

`any_connection::connect` already does this. This is no longer relevant.
