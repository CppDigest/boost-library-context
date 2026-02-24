# #15 removed deprecated std::allocator<void> [Closed]

> Username: tobias-loew  
> Created at: 2018-10-29 08:26:47 UTC  
> Updated at: 2020-05-22 01:31:54 UTC  
> Closed at: 2020-05-21 15:18:40 UTC  
> Url: https://github.com/boostorg/bimap/pull/15  

replaced std::allocator<void> by std::allocator<char>  
use std::allocator_traits for rebinding  
  
usage of std::allocator_traits is guarded by BOOST_NO_CXX11_ALLOCATOR macro

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-09 16:39:18 UTC  
> Url: https://github.com/boostorg/bimap/pull/15#issuecomment-596640958  

Hi,  
  
With C++20 and gcc 10, this pull request will become mandatory. I guess it should be considered for merging.  
  
Cheers,  
Romain

---

## Comment 2

> Username: jwakely  
> Created_at: 2020-03-30 11:50:30 UTC  
> Updated_at: 2020-03-30 12:00:25 UTC  
> Url: https://github.com/boostorg/bimap/pull/15#issuecomment-605953408  

N.B. the explicit specialization of `std::allocator<void>` is deprecated in C++17 and gone in C++20, but that just means that code using `std::allocator<void>` will use the primary template instead. It shouldn't be necessary to stop referring to `std::allocator<void>` as a placeholder for a real allocator (i.e. what the Networking TS calls a ProtoAllocator).

---

## Comment 3

> Username: ramsay-vibes  
> Created_at: 2020-04-29 07:26:24 UTC  
> Url: https://github.com/boostorg/bimap/pull/15#issuecomment-621034705  

When building with Boost 1.72, Visual Studio 2019 16.5.4 and -std::c++17, the following simple example fails to build due to std::allocator<void> deprecation warnings.  
  
```  
void repo(int id)  
{  
    boost::bimap<int, int> lookup;  
    auto iter = lookup.left.find(id);  
    if (iter != lookup.left.end())  
    {  
        printf("%d", iter->get_right());  
    }  
}  
```  
  
I patched this pull request into a local copy of Boost 1.72 and it fixes the compiler warning.  
  
Is there any chance that this will soon be merged into a future release of Boost?

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-05-21 15:18:40 UTC  
> Updated_at: 2020-05-22 01:31:54 UTC  
> Url: https://github.com/boostorg/bimap/pull/15#issuecomment-632144735  

Addressed in 83f4f27f3a3744bb27687362dc3ca73453a66090 (and 6fba6e5de3a5d9b0675a875fdac8aad9238d6dd8).

---

## Comment 5

> Username: jwakely  
> Created_at: 2020-05-21 22:30:08 UTC  
> Updated_at: 2020-05-21 22:33:02 UTC  
> Url: https://github.com/boostorg/bimap/pull/15#issuecomment-632379058  

~~@glenfe if you don't use `allocator_traits` to rebind then it's still wrong. Using `allocator<void>` is not the problem. Using nested members of `std::allocator` directly is the problem, because those members are going away and using `allocator_traits` is now required (and has been the right way to do it since 2011).~~ I see that part was addressed in 6fba6e5de3a5d9b0675a875fdac8aad9238d6dd8, sorry for the noise.

---

## Comment 6

> Username: glenfe  
> Created_at: 2020-05-22 00:52:25 UTC  
> Url: https://github.com/boostorg/bimap/pull/15#issuecomment-632419210  

:) https://www.boost.org/doc/libs/master/libs/core/doc/html/core/allocator_access.html is our  alternative to `allocator_traits`.  (We also don't care if users specialize them).

---
