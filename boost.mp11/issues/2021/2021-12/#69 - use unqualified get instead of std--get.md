# #69 - use unqualified get instead of std::get [Closed]

> Username: marcorubini  
> Created at: 2021-12-12 18:43:52 UTC  
> Updated at: 2021-12-14 22:53:44 UTC  
> Closed at: 2021-12-14 22:53:44 UTC  
> Url: https://github.com/boostorg/mp11/issues/69  

this change would allow tuple operations to be called on user defined type, as long as a get overload can be found using unqualified syntax. As far as I understand, currently the only way to use tuple operations on user defined types is to extend the std namespace (overload std::get), which is ugly.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-14 22:53:44 UTC  
> Url: https://github.com/boostorg/mp11/issues/69#issuecomment-994116639  

Changed to unqualified in https://github.com/boostorg/mp11/commit/edba13573919a8a8fb8410360e3a3a0c18867c0b.
