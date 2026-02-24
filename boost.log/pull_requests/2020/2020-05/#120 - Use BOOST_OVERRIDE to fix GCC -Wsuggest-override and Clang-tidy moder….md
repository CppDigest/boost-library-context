# #120 Use BOOST_OVERRIDE to fix GCC -Wsuggest-override and Clang-tidy moder… [Merged]

> Username: EugeneZelenko  
> Created at: 2020-05-04 03:31:12 UTC  
> Updated at: 2020-05-04 14:22:13 UTC  
> Merged at: 2020-05-04 14:20:08 UTC  
> Closed at: 2020-05-04 14:20:08 UTC  
> Url: https://github.com/boostorg/log/pull/120  

…nize-use-override warnings.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-05-04 10:29:17 UTC  
> Url: https://github.com/boostorg/log/pull/120#issuecomment-623385017  

Destructors don't need to be marked.

---

## Comment 2

> Username: EugeneZelenko  
> Created_at: 2020-05-04 13:49:49 UTC  
> Url: https://github.com/boostorg/log/pull/120#issuecomment-623475728  

Somehow GCC -Wsuggest-override doesn't cover destructors, but Clang-tidy modernize-use-override warnings does.  
  
Anyway destructors are not different from methods in respect to `virtual/override/final`.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-05-04 14:13:01 UTC  
> Url: https://github.com/boostorg/log/pull/120#issuecomment-623488817  

They are different in that they always override if the base destructor is virtual. The problem with the markup is that it *requires* the base destructor to be virtual, which can be a problem if this changes at some point.

---

## Comment 4

> Username: EugeneZelenko  
> Created_at: 2020-05-04 14:13:56 UTC  
> Url: https://github.com/boostorg/log/pull/120#issuecomment-623489405  

Same would happen with methods.

---

## Comment 5

> Username: EugeneZelenko  
> Created_at: 2020-05-04 14:22:13 UTC  
> Url: https://github.com/boostorg/log/pull/120#issuecomment-623494368  

Thank you for help!

---
