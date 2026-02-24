# #2925 - Moving *this before calling get_immediate_executor() causes undefined behavior [Closed]

> Username: Jackarain  
> Created at: 2024-09-04 13:46:39 UTC  
> Updated at: 2024-09-04 15:48:01 UTC  
> Closed at: 2024-09-04 15:48:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2925  

In the following code, std::move(*this) is used before calling get_immediate_executor(). This can result in undefined behavior, as the this object may become invalid after the move operation.  
  
https://github.com/boostorg/beast/blob/21545dbcaf22d21ba72c0021dae0ee85c7004fc5/include/boost/beast/core/impl/basic_stream.hpp#L328-L330

---

## Comment 1

> Username: ashtum  
> Created at: 2024-09-04 14:01:23 UTC  
> Updated at: 2024-09-04 14:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2925#issuecomment-2329154536  

@Jackarain Thanks for reporting this. I can confirm that this is a bug, as the order of evaluation of function arguments is unspecified and move can happen before dereferencing `this`.

---

## Comment 2

> Username: Jackarain  
> Created at: 2024-09-04 14:16:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2925#issuecomment-2329191526  

感谢您的回复，以及迅速的作出修复，感谢

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-09-04 15:18:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2925#issuecomment-2329353332  

Great work, @Jackarain :) thanks.
