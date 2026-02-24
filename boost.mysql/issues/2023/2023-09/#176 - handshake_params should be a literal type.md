# #176 - handshake_params should be a literal type [Closed]

> Username: anarthal  
> Created at: 2023-09-25 10:16:22 UTC  
> Updated at: 2024-08-10 16:08:11 UTC  
> Closed at: 2024-08-10 16:08:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/176  

There is no reason why handshake_params couldn't have all member functions constexpr.

---

## Comment 1

> Username: marco-langer  
> Created at: 2023-12-04 19:47:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/176#issuecomment-1839365313  

The type `handshake_params` seems to have no class invariants and is just a collection of data ("pod"). Usually one would argue that it should be a struct instead of a class, see also [C++ Core Guideline C.2](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-struct).   
  
That would make the need for constexpr getter/setter obsolete but would break the current API.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-12-04 20:48:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/176#issuecomment-1839452101  

I'm actually writing a type-erased connection which is probably gonna become the new standard connection - and uses a raw struct for this purpose.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-08-10 16:08:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/176#issuecomment-2282196410  

Boost 1.87 will make `any_connection` stable, and it's the way to go. This is no longer relevant.
