# #166 - support input_iterator_tag in params and segments [Open]

> Username: alandefreitas  
> Created at: 2022-04-15 16:02:49 UTC  
> Updated at: 2022-10-13 18:15:36 UTC  
> Url: https://github.com/boostorg/url/issues/166  

Functions such as  
https://github.com/CPPAlliance/url/blob/3b6e2afafcd8733ec453c2595e1d434913eed335/include/boost/url/params.hpp#L174  
could be implemented for `std::input_iterator_tag`.  
  
Since we pass through the parameters twice, it would require us to use an extra heap allocation for a `std::string` with the params before calling `edit_params`. This should be documented.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-04-15 16:58:00 UTC  
> Updated at: 2022-04-15 16:58:13 UTC  
> Url: https://github.com/boostorg/url/issues/166#issuecomment-1100228254  

`static_url` guarantees no dynamic allocation for modification operations, but we can't offer that for `assign` with input iterators, so this should be documented as such if we implement it. The strong exception safety guarantee has to hold.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-11 23:55:58 UTC  
> Url: https://github.com/boostorg/url/issues/166#issuecomment-1243072294  

I love the idea of rendering the range into a `recycled_ptr<std::string>` :) This is low priority since its an added feature (and unlikely to be missed)

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-10-13 16:47:06 UTC  
> Url: https://github.com/boostorg/url/issues/166#issuecomment-1277902867  

> I love the idea of rendering the range into a recycled_ptr<std::string>   
  
Is this still your most recent idea?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-10-13 18:15:36 UTC  
> Url: https://github.com/boostorg/url/issues/166#issuecomment-1277997553  

we can just leave this open and add it to some list of things to consider after the first release or two
