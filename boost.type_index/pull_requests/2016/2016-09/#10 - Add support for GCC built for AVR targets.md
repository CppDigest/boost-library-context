# #10 Add support for GCC built for AVR targets [Closed]

> Username: felipealmeida  
> Created at: 2016-09-29 22:13:06 UTC  
> Updated at: 2016-10-02 17:42:49 UTC  
> Closed at: 2016-10-02 15:36:51 UTC  
> Url: https://github.com/boostorg/type_index/pull/10  

This includes a special case for type_index library for AVR targets  
which use unsigned int as std::size_t type.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-09-30 17:49:45 UTC  
> Url: https://github.com/boostorg/type_index/pull/10#issuecomment-250808808  

This seems to perfectly work, but I've just realized that `std::size_t` heavily depends on platform...  
  
I'd rather replace all the `std::size_t` in that header file with `unsigned long int`. I'll try to fix that in a month. If you're willing to help - give me a hint and I'd appreciate a pull request.  
  
Anyway, thank you for pointing me at the issue.

---

## Comment 2

> Username: felipealmeida  
> Created_at: 2016-10-01 02:44:10 UTC  
> Url: https://github.com/boostorg/type_index/pull/10#issuecomment-250887911  

How can i help ? Unfortunately I don't have access to windows compilers ATM.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2016-10-02 07:34:54 UTC  
> Url: https://github.com/boostorg/type_index/pull/10#issuecomment-250958406  

Could you please try out the latest develop version of the library? I've probably fixed the issue in b669f02, but I'd like to be certain.

---

## Comment 4

> Username: felipealmeida  
> Created_at: 2016-10-02 15:36:51 UTC  
> Url: https://github.com/boostorg/type_index/pull/10#issuecomment-250977358  

Yes, it works.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2016-10-02 17:42:49 UTC  
> Url: https://github.com/boostorg/type_index/pull/10#issuecomment-250984060  

Thanks for finding the issue and testing!

---
