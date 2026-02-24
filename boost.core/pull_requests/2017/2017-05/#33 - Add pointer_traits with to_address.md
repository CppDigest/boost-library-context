# #33 Add pointer_traits with to_address [Merged]

> Username: glenfe  
> Created at: 2017-05-16 11:58:47 UTC  
> Updated at: 2017-05-17 13:21:34 UTC  
> Merged at: 2017-05-17 13:21:21 UTC  
> Closed at: 2017-05-17 13:21:21 UTC  
> Url: https://github.com/boostorg/core/pull/33  

Still working on documentation.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-05-16 13:19:09 UTC  
> Url: https://github.com/boostorg/core/pull/33#issuecomment-301779409  

So you decided to call it `to_raw_pointer` after all. Do we need it now that the implementation of `pointer_traits` doesn't use it?  
  
`boost::pointer_traits` should be present even if `std::pointer_traits` isn't, and should work the same way, except of course `rebind` if aliases are unavailable. We should have a convention for that... `rebind_trait`? `rebind_type`?

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-05-16 14:01:22 UTC  
> Url: https://github.com/boostorg/core/pull/33#issuecomment-301791781  

I'm on the fence between: Keeping it at all (with any name, `to_raw_pointer`, `to_address`, `get_address`) or dropping it. (`pointer_to_address` isn't too long, but the convention of `to_string` is nice, one less type mentioned in identifier).  
  
I wish `rebind` were specified as types containing a `type` or `other` member instead of aliases, just for compatibility sake. If `std::pointer_traits` isn't available, should we just provide the `T*` specialization of `pointer_traits`, along with a `rebind` containing `other`?

---

## Comment 3

> Username: glenfe  
> Created_at: 2017-05-16 14:05:27 UTC  
> Url: https://github.com/boostorg/core/pull/33#issuecomment-301792993  

I've found myself writing something like the following more than once: https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/allocate_shared_array.hpp#L134-L144  
  
Were you thinking of something like that, but where the traits template is also specified (i.e. pointer_traits, allocator_traits)?

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-05-16 14:14:52 UTC  
> Url: https://github.com/boostorg/core/pull/33#issuecomment-301795768  

> If std::pointer_traits isn't available, should we just provide the T* specialization of pointer_traits, along with a rebind containing other?  
  
I was thinking that we should provide a fully functional `pointer_traits` if the standard one isn't available, but with `rebind` replaced with a struct with some other name (`rebind_type`?), to not conflict with the standard one. We can supply that other name in `boost::pointer_traits` in the other path as well, so that code can use it without ifdefs.  
  
Incidentally, as written `boost::pointer_traits<>::rebind` returns `std::pointer_traits<>`, so we'll have to massage that in either case.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-05-16 14:16:40 UTC  
> Url: https://github.com/boostorg/core/pull/33#issuecomment-301796354  

`template<class U> using rebind = mp_rename<std::pointer_traits<T>::rebind<U>, boost::pointer_traits>;` :-)

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-05-16 14:18:38 UTC  
> Url: https://github.com/boostorg/core/pull/33#issuecomment-301796926  

Ah wait, this `rebind` returns the pointer type. Never mind.

---

## Comment 7

> Username: glenfe  
> Created_at: 2017-05-17 13:21:26 UTC  
> Url: https://github.com/boostorg/core/pull/33#issuecomment-302088036  

Merged after Peter's review and approval on Slack.

---
