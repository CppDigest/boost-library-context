# #72 Fix access to first element of empty vector [Merged]

> Username: Flamefire  
> Created at: 2021-12-10 16:57:30 UTC  
> Updated at: 2021-12-10 20:40:21 UTC  
> Merged at: 2021-12-10 20:40:19 UTC  
> Closed at: 2021-12-10 20:40:19 UTC  
> Url: https://github.com/boostorg/locale/pull/72  

Supersedes #60. Using original commit message (part) of @OznOg  
  
Trying to access tmp[0] causes a crash on Fedora when assertion on STL are enabled.  
  
`/usr/include/c++/10/bits/stl_vector.h:1045: std::vector<_Tp, _Alloc>::reference std::vector<_Tp, _Alloc>::operator[](std::vector<_Tp, _Alloc>::size_type) [with _Tp = unsigned char; _Alloc = std::allocator<unsigned char>; std::vector<_Tp, _Alloc>::reference = unsigned char&; std::vector<_Tp, _Alloc>::size_type = long unsigned int]: Assertion '__builtin_expect(__n < this->size(), true)' failed.`  
  
Fix is to never have an empty vector as ICU sort keys include the NULL terminator, hence we need at least `length + 1` bytes which means the vector has at least 1 element: The NULL terminator  
  
@OznOg Would you mind verifying this works for you?

---

## Comment 1

> Username: OznOg  
> Created_at: 2021-12-10 19:17:46 UTC  
> Url: https://github.com/boostorg/locale/pull/72#issuecomment-991230885  

I'm affraid it would be hard for me, I don't have the original setup anymore.  
That said, as you patch makes the vector to be at least 1 element, one must admit that accessing &tmp[0] will always be valid.

---
