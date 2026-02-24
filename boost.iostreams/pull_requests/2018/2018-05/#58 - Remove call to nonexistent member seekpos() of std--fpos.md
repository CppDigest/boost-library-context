# #58 Remove call to nonexistent member seekpos() of std::fpos [Closed]

> Username: Cheney-W  
> Created at: 2018-05-31 10:40:33 UTC  
> Updated at: 2018-06-05 01:50:53 UTC  
> Closed at: 2018-06-05 01:50:53 UTC  
> Url: https://github.com/boostorg/iostreams/pull/58  

1. Boost-iostreams failed with error C2039: 'seekpos': is not a member of 'std::fpos<_Mbstatet>'.  
2. See http://eel.is/c++draft/fpos -- to get to an offset you can convert to int; now there is no seekpos member.

---

## Comment 1

> Username: BillyONeal  
> Created_at: 2018-06-01 06:01:10 UTC  
> Url: https://github.com/boostorg/iostreams/pull/58#issuecomment-393769352  

Cheney, I think this change is incorrect; and that the correct change is that previously accepted here: https://github.com/boostorg/iostreams/pull/57

---
