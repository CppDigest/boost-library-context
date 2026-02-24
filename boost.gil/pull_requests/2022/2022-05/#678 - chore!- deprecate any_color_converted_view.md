# #678 chore!: deprecate any_color_converted_view [Merged]

> Username: striezel  
> Created at: 2022-05-25 13:40:22 UTC  
> Updated at: 2022-05-26 22:14:02 UTC  
> Merged at: 2022-05-26 06:58:19 UTC  
> Closed at: 2022-05-26 06:58:19 UTC  
> Url: https://github.com/boostorg/gil/pull/678  

### Description  
  
`color_converted_view` has the same implementation and can be used as a replacement, providing the same functionality.  
  
### References  
  
This builds on https://github.com/boostorg/gil/pull/660 by @marco-langer.  
Since there needs to be a deprecation notice first before removing a feature, here it is.  
  
Due to the fact that the switch to C++14 has been announced in #677, we can now officially use the [`[[deprecated]]` attribute](https://en.cppreference.com/w/cpp/language/attributes/deprecated) which was standardized in C++14.  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-05-25 21:18:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/678#pullrequestreview-985482478  

Looks good!  
  
This is initial part of deprecating the `any_color_converted_view` and as @marco-langer pointed in https://github.com/boostorg/gil/pull/660#issuecomment-1138183545 there are more changes to follow.

---
