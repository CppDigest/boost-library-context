# #112 Fix deprecated-dynamic-exception-spec warning [Closed]

> Username: jonesmz  
> Created at: 2021-07-14 18:20:36 UTC  
> Updated at: 2025-09-27 10:50:32 UTC  
> Closed at: 2025-09-27 10:50:32 UTC  
> Url: https://github.com/boostorg/program_options/pull/112  



---

## Comment 1

> Username: vprus  
> Created_at: 2021-07-15 10:47:46 UTC  
> Url: https://github.com/boostorg/program_options/pull/112#issuecomment-880594112  

Thanks for the PR?  
Would it change the C++ level the library requires, though? I think it still compiles without C++ 11.

---

## Comment 2

> Username: jonesmz  
> Created_at: 2021-07-15 20:20:41 UTC  
> Updated_at: 2021-07-15 20:20:54 UTC  
> Url: https://github.com/boostorg/program_options/pull/112#issuecomment-880987365  

`noexcept` keyword was introduced in C++11.  
  
`noexcept` is not 100% identical to `throw()`, but for the way boost program options uses it, they are equivalent.  
  
Someone could define a `BOOST_PROGRAM_OPTIONS_NOTHROW` macro, which would use either `throw()` or `noexcept` based on the C++ language version detected at compile time.  
  
Just to be clear, I don't intend to revisit this PR, or add support for older versions of C++ earlier than C++11. It's not that I think that's a bad idea, it's just beyond the scope of the time I have available for this work.  
  
Someone else can add new commits to this PR, or this PR can be closed and replaced with a different PR.

---

## Comment 3

> Username: vprus  
> Created_at: 2025-09-27 10:50:32 UTC  
> Url: https://github.com/boostorg/program_options/pull/112#issuecomment-3341500482  

This has been essentially done in 1d7cceff6c8a5d40593776d7578a984852df5b75

---
