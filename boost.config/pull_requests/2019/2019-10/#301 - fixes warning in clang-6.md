# #301 fixes warning in clang-6 [Merged]

> Username: HDembinski  
> Created at: 2019-10-19 12:07:02 UTC  
> Updated at: 2019-10-24 17:09:03 UTC  
> Merged at: 2019-10-20 11:55:09 UTC  
> Closed at: 2019-10-20 11:55:09 UTC  
> Url: https://github.com/boostorg/config/pull/301  

Addresses #300

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-10-20 11:56:43 UTC  
> Url: https://github.com/boostorg/config/pull/301#issuecomment-544245589  

Thanks for that.  
  
Can you please report upstream to clang, as it seems unreasonable for `__has_cpp_attribute(nodiscard)` to report `true` when `-pedantic -Werror` will result in a compiler error!

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-10-21 11:59:48 UTC  
> Url: https://github.com/boostorg/config/pull/301#issuecomment-544481817  

I will try to report it upstream. Registering on the LLVM bug-tracker is surprisingly complicated, apparently they are having problems with spam.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2019-10-21 13:15:27 UTC  
> Updated_at: 2019-10-21 13:16:05 UTC  
> Url: https://github.com/boostorg/config/pull/301#issuecomment-544509217  

Actually, maybe we misunderstood the usage of `__has_cpp_attribute`. Despite the name, it does not only return true and false, but the date when the feature was added to the C++ standard.  
https://en.cppreference.com/w/cpp/feature_test  
  
So we maybe we should test like this?  
  
```  
#if __has_cpp_attribute(nodiscard)  < __cplusplus  
#define BOOST_ATTRIBUTE_NODISCARD [[nodiscard]]  
#else  
#define BOOST_ATTRIBUTE_NODISCARD  
#endif  
```

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-10-23 17:50:56 UTC  
> Url: https://github.com/boostorg/config/pull/301#issuecomment-545560817  

The problem is that msvc still defines __cplusplus to 199711 :(

---

## Comment 5

> Username: HDembinski  
> Created_at: 2019-10-24 14:50:13 UTC  
> Url: https://github.com/boostorg/config/pull/301#issuecomment-545955157  

Ah, ok. Then we should probably keep it as is. :)

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-10-24 17:09:03 UTC  
> Url: https://github.com/boostorg/config/pull/301#issuecomment-546013669  

>Ah, ok. Then we should probably keep it as is. :)  
  
That's the easy solution, but I suspect that technically you are correct and this may all come back to bite us whatever....

---
