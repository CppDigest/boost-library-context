# #84 Add BOOST_SYSTEM_CURRENT_LOCATION_PTR [Open]

> Username: ecatmur  
> Created at: 2022-07-14 17:26:29 UTC  
> Updated at: 2025-08-24 08:05:18 UTC  
> Url: https://github.com/boostorg/system/pull/84  

Allows passing current location to error_code ctor without requiring a separate static local variable.  
  
Implementation uses __builtin_source_location() on clang and gcc returning a pointer to std::source_location::__impl, which we verify is layout-compatible with boost::source_location.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-02-03 18:30:15 UTC  
> Url: https://github.com/boostorg/system/pull/84#issuecomment-1925423129  

I'm looking at this and wondering where we want to go with it.  
  
Ideally, I'd like to make this macro the default argument of the `source_location const*` parameter everywhere. Not sure when, though.  
  
Looking at the codegen, the first lambda path, the one passing the current location as an argument to the lambda, generates a guard variable. I don't see a way to avoid that, and for me, this kind of rules it out from being silently applied by default.  
  
I suppose we could just use the other one, clearing the function name.  
  
The Clang path seems a bit dubious; while clang-cl supports `__builtin_source_location()`, it doesn't work, because in the MS STL, `std::source_location` doesn't have an `__impl` member.  
  
Also, Clang using libstdc++ would use its `std::source_location`, which would make `__impl` inaccessible, but the test doesn't seem to account for this, it only looks at the compiler.  
  
If we consider the _PTR macro an implementation detail that's only used as a default argument, I think we can dispense with the type safety and just use an ordinary pointer.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-02-04 07:22:16 UTC  
> Url: https://github.com/boostorg/system/pull/84#issuecomment-1925609103  

> I don't see a way to avoid that  
  
Maybe we could just do a little data race: https://godbolt.org/z/fq6ETGqcP

---
