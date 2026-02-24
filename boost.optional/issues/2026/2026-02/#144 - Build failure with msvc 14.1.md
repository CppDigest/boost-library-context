# #144 - Build failure with msvc 14.1 [Closed]

> Username: grisumbras  
> Created at: 2026-02-13 19:12:57 UTC  
> Updated at: 2026-02-16 20:16:28 UTC  
> Closed at: 2026-02-16 20:16:28 UTC  
> Url: https://github.com/boostorg/optional/issues/144  

See https://drone.cpp.al/boostorg/json/1981/47/2

---

## Comment 1

> Username: akrzemi1  
> Created at: 2026-02-16 06:12:24 UTC  
> Url: https://github.com/boostorg/optional/issues/144#issuecomment-3906639812  

Can you check it now? I think I patched the MSVC 14.1 bug.

---

## Comment 2

> Username: grisumbras  
> Created at: 2026-02-16 11:14:41 UTC  
> Url: https://github.com/boostorg/optional/issues/144#issuecomment-3907884370  

The error changed: https://drone.cpp.al/boostorg/json/1985/47/2  
  
> C:\drone\boost-root\boost/optional/detail/union_optional.hpp(275): error C2280: 'boost::optional_detail::fallback_guarded_storage<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>::fallback_guarded_storage(boost::optional_detail::fallback_guarded_storage<std::basic_string<char,std::char_traits<char>,std::allocator<char>>> &&)': **attempting to reference a deleted function**

---

## Comment 3

> Username: akrzemi1  
> Created at: 2026-02-16 12:25:39 UTC  
> Url: https://github.com/boostorg/optional/issues/144#issuecomment-3908208628  

Thank you for checking.   
I am sorry for this patchy process, but I cannot reproduce this, even on an MSVC 14.1 job.  
I have applied another patch attempt. Can you run your test again?

---

## Comment 4

> Username: grisumbras  
> Created at: 2026-02-16 13:42:55 UTC  
> Url: https://github.com/boostorg/optional/issues/144#issuecomment-3908571783  

The error changed yet again: https://drone.cpp.al/boostorg/json/1986/47/2  
  
> C:\drone\boost-root\boost/optional/detail/union_optional.hpp(280): error C2248: 'boost::optional_detail::fallback_guarded_storage<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>::fallback_guarded_storage': **cannot access private member** declared in class 'boost::optional_detail::fallback_guarded_storage<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>'  
  
It's interesting that you can't reproduce. I'll try making a MCVE.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2026-02-16 14:44:31 UTC  
> Url: https://github.com/boostorg/optional/issues/144#issuecomment-3908868493  

Can you make a yet another run?  
This time I expect either a success or a linker error.  
If it fails this time, I will simply exclude MSVC 14.1 from the new optional implementation (I will fall back to the old one).  
This is definitely a compiler bug. I am now trying to figure out if any workaround is possible.

---

## Comment 6

> Username: grisumbras  
> Created at: 2026-02-16 20:16:28 UTC  
> Url: https://github.com/boostorg/optional/issues/144#issuecomment-3910365258  

It worked! Thank you for the quick fix.
