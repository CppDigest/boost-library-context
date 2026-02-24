# #145 Use BOOST_OVERRIDE [Closed]

> Username: EugeneZelenko  
> Created at: 2020-05-29 02:44:39 UTC  
> Updated at: 2020-05-29 13:11:20 UTC  
> Closed at: 2020-05-29 07:59:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/145  

Use BOOST_OVERRIDE to fix GCC -Wsuggest-override and Clang-tidy modernize-use-override warnings.  
Fix Clang -Wextra-semi and Clang-tidy readability-container-size-empty and readability-redundant-control-flow warnings.  
Alphabetical sort of STL headers.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-05-29 07:59:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/145#issuecomment-635824030  

I'm not going to add `override` to destructors, this markup is pointless. Removing `return`s is also pointless, but ok. Replacing `size` with `empty` is useful. I don't see the need to reorder includes; if there is a reason, please describe it.  
  
This PR is doing many things at once. Please extract the useful stuff in separate PRs.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-05-29 08:03:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/145#issuecomment-635825966  

> I don't see the need to reorder includes; if there is a reason, please describe it.  
  
Sorry, I missed that you ordered them in alphabetical order. No, I'm not doing that.

---
