# #45 - std::unique_ptr and MSVC build failed [Closed]

> Username: Malibushko  
> Created at: 2020-07-17 12:49:03 UTC  
> Updated at: 2020-12-21 16:07:01 UTC  
> Closed at: 2020-12-21 16:07:01 UTC  
> Url: https://github.com/boostorg/pfr/issues/45  

Build failed on MSVC with the following code (but it does not on GCC or clang)  
```  
struct UniquePtr {  
    std::unique_ptr<int> a;  
};  
static_assert (boost::pfr::tuple_size_v<UniquePtr> == 1);  
```  
To be more precise the following static_asserts trigger:  
1) ```Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.```  
2) ```Boost.PFR: If there's no other failed static asserts then something went wrong. Please report this issue to the github along with the structure you're reflecting.```  
See more CI output on https://github.com/Malibushko/magic_get_test   
  
Should it work like that or is this a bug?

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-08-20 12:52:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/45#issuecomment-677645861  

There's no known to me workaround for this issue of MSVC. I hope that they will fix it in their compiler, but the issue was not reported and to report it the example should be minimized to a few lines without any includes. I'd really appreciate if you could do that (I'll have no simple access to the MSVC compiler in many months).

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-12-21 16:07:01 UTC  
> Url: https://github.com/boostorg/pfr/issues/45#issuecomment-749051552  

That issue was fixed somewhere around version 2.0.0, can not reproduce it in 2.0.1 release.  
  
Added your test into the library tests, so the issue would not accidently come back.  
  
Many thanks for the report!
