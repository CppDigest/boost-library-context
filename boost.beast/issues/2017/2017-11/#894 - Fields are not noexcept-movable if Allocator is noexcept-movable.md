# #894 - Fields are not noexcept-movable if Allocator is noexcept-movable [Closed]

> Username: djarek  
> Created at: 2017-11-18 19:16:30 UTC  
> Updated at: 2017-11-29 04:09:47 UTC  
> Closed at: 2017-11-29 04:09:47 UTC  
> Url: https://github.com/boostorg/beast/issues/894  

basic_fields should conditionally be noexcept movable to enable some container optimizations based on noexcept.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-27 22:02:28 UTC  
> Url: https://github.com/boostorg/beast/issues/894#issuecomment-347343548  

is this still relevant given the other pull request?

---

## Comment 2

> Username: djarek  
> Created at: 2017-11-27 22:05:54 UTC  
> Url: https://github.com/boostorg/beast/issues/894#issuecomment-347344451  

The scope changed a little, because I assumed that allocators didn't have to be noexcept-movable (they have to, according to the Allocator requirements).
