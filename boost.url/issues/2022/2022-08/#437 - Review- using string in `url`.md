# #437 - Review: using string in `url` [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 04:57:39 UTC  
> Updated at: 2022-09-03 20:10:19 UTC  
> Closed at: 2022-09-03 20:10:19 UTC  
> Url: https://github.com/boostorg/url/issues/437  

> In your non-view url object, you seem to allocate storage using  
new char[]. Why not use a std::string? One benefit of a std::string  
is SBO. Another is that you could provide a move constructor from  
a string.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-23 22:28:58 UTC  
> Url: https://github.com/boostorg/url/issues/437#issuecomment-1224954014  

if we do this then we can't put a table at the end of the allocation anymore. it has to be thought out. we need to know the benefits versus the costs.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-24 03:15:43 UTC  
> Url: https://github.com/boostorg/url/issues/437#issuecomment-1225131071  

> if we do this then we can't put a table at the end of the allocation anymore.   
  
Yes. Although we're probably not going back to that design.  
  
Not including <string> could be a good reason to avoid it, but this ship has sailed.  
  
> it has to be thought out. we need to know the benefits versus the costs.  
  
I guess the main benefit is SSO/SBO and simpler code. But we can also achieve that if we want.  
  
The move constructor from a string doesn't look like a huge benefit to me. It's technically true though.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-03 20:10:19 UTC  
> Url: https://github.com/boostorg/url/issues/437#issuecomment-1236191994  

`std::string` would cause value-initialization on every call to `string::resize`... no thanks.
