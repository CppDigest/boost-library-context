# #303 - std::aligned_storage raises deprecation warning with the latest gcc 13 and C++23 (to be released in about 1 month and a half) [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2023-03-21 17:18:59 UTC  
> Updated at: 2025-02-07 14:34:47 UTC  
> Closed at: 2024-08-28 10:59:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/303  

Hi,  
  
We try to compile a project which depends on boost::fiber, using a bleeding edge toolchain (ie using gcc 13 which will be released in 1 month and a half) and using C++23. In this version, libstdc++ has explicitly deprecated std::aligned_storage, as required by the standard.  
  
I am not sure how you want to cope with this. You may:  
 - just ignore the deprecation (for now)  
 - somehow replace this by a union type having two member, one would be a single char, and another one would be the actual type you try to store.  
 - look at other replacements that either the standard or the folks in the C++ communitee recommand. For example one possible replacement using C++17's std::byte is being showed here (yet it still has the drawbacks of std::aligned_storage): https://stackoverflow.com/questions/71828288/why-is-stdaligned-storage-to-be-deprecated-in-c23-and-what-to-use-instead  
   
 Cheers,  
 Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2023-03-21 17:34:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/303#issuecomment-1478319567  

Note: there is also boost::aligned_storage defined in `boost/type_traits/aligned_storage.hpp`, yet I am not sure this is actually the direction the standard wanted you to follow when deprecating the std one !

---

## Comment 2

> Username: olk  
> Created at: 2024-08-28 10:59:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/303#issuecomment-2314997212  

Shoiuld be fixed by PR #312

---

## Comment 3

> Username: francoisk  
> Created at: 2025-02-07 14:34:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/303#issuecomment-2643113262  

I'm still getting these deprecation warnings in the following places:  
  
https://github.com/boostorg/fiber/blob/048952639ad715010b27cf705b0611340a563f67/include/boost/fiber/buffered_channel.hpp#L359  
  
https://github.com/boostorg/fiber/blob/048952639ad715010b27cf705b0611340a563f67/include/boost/fiber/unbuffered_channel.hpp#L395
