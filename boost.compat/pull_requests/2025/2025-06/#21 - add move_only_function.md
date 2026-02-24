# #21 add move_only_function [Merged]

> Username: cmazakas  
> Created at: 2025-06-11 17:45:50 UTC  
> Updated at: 2025-06-28 18:11:04 UTC  
> Merged at: 2025-06-28 14:52:06 UTC  
> Closed at: 2025-06-28 14:52:06 UTC  
> Url: https://github.com/boostorg/compat/pull/21  

@pdimov pinging you directly to make sure you see this  
  
Implementation of `move_only_function`. Correctly uses SBO and handles the double-indirection problem caused by:  
```cpp  
move_only_function<int() noexcept> f1;  
move_only_function<int()> f2( std::move( f1 ) );  
```  
  
I included docs too.  
  
Aside from more and more tests, the only thing I know that's definitively missing from the quality of implementation is passing small, trivial arguments by value instead of forwarding them as `&`. But this is something that I figured we can always add later.  
  
Closes https://github.com/boostorg/compat/issues/5

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-06-11 18:20:58 UTC  
> Url: https://github.com/boostorg/compat/pull/21#issuecomment-2963769107  

msvc-14.1 doesn't like you.

---
