# #32 Update get_pointer to support all fancy pointers [Closed]

> Username: glenfe  
> Created at: 2017-05-10 11:27:30 UTC  
> Updated at: 2017-05-11 23:08:06 UTC  
> Closed at: 2017-05-11 14:57:09 UTC  
> Url: https://github.com/boostorg/core/pull/32  

This is the alternative pull request which updates `get_pointer` instead of adding `to_raw_pointer`. The other pull request is #31 which adds `to_raw_pointer` to Core.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-05-10 13:56:16 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300489623  

Would like to hear @igaztanaga on this (regarding the usefulness of `get_pointer` for going from fancy pointer to raw pointer). I don't see `get_pointer` actually being used in his libraries, so maybe we already have another similar facility lurking somewhere.  
  
I see two potential problems with this implementation; first, `std::pointer_traits<P>::element_type` is defined for many non-pointer-like types, so the template won't SFINAE. I'm not sure whether this matters, but it's a behavior change from what we had.  
  
Second, operator-> is undefined when the pointer is 0 (for both unique_ptr and shared_ptr). This is arguably a defect in the standard, but even if the standard is fixed, many user-defined pointers follow this practice. (Incidentally, we're missing get_pointer tests for 0.)

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-05-10 15:11:45 UTC  
> Updated_at: 2017-05-10 15:12:36 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300513742  

I see he also has `to_raw_pointer` defined similarly (as in pull request #31 which was my alternative to this one).  
  
I see one in Boost.Move in [<boost/move/detail/to_raw_pointer.hpp>](https://github.com/boostorg/move/blob/master/include/boost/move/detail/to_raw_pointer.hpp). Similarly in Boost.Intrusive (and Boost.Container uses it from Boost.Intrusive).

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2017-05-11 07:09:11 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300702419  

Yes, in my libraries I use to_raw_pointer-like utilities. Those are needed, for instance, when trying to get a raw pointer to call allocator_traits::construct, which requires a raw pointer, from a fancy pointer. In this case, non-null pointers are guaranteed by the caller.

---

## Comment 4

> Username: glenfe  
> Created_at: 2017-05-11 14:57:22 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300815756  

Closing, to focus on #31.

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-05-11 15:07:16 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300818872  

> Closing, to focus on #31.  
  
Why?

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-05-11 15:51:29 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300833133  

To avoid the issue with `get_pointer` supporting NULL.

---

## Comment 7

> Username: Lastique  
> Created_at: 2017-05-11 15:59:42 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300835778  

> To avoid the issue with `get_pointer` supporting NULL.  
  
I'm not sure I understand. Whether it is implemented in `get_pointer` or `to_raw_pointer`, we face the same problem with null smart-pointers. Whatever the solution is, it can be done with the same effect in either `get_pointer` or `to_raw_pointer`.  
  
My preference is `get_pointer` because I don't like duplicating tools, and currently I can't see the difference between the two functions.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-05-11 16:28:40 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300843939  

With `to_raw_pointer`, we have the option of disallowing NULL pointers, as it's used to construct an object at a location that comes in the form of `Allocator::pointer`.

---

## Comment 9

> Username: Lastique  
> Created_at: 2017-05-11 16:40:25 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300847115  

On 05/11/17 19:28, Peter Dimov wrote:  
> With |to_raw_pointer|, we have the option of disallowing NULL pointers,  
> as it's used to construct an object at a location that comes in the form  
> of |Allocator::pointer|.  
  
Well, the comment says "get_pointer(p) extracts a ->* capable pointer   
from p", so I would say we have the same option with `get_pointer`.

---

## Comment 10

> Username: glenfe  
> Created_at: 2017-05-11 16:45:42 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300848507  

> Why?  
  
@Lastique  Peter and I were talking about this on the C++ Slack: The piece that I was missing was `get_pointer` supporting null pointers, while the precondition for `to_raw_pointer` being that the pointer is not null. The motivating use case is the argument to `construct` for which `addressof(*p)` is unsuitable when `*p` has no object constructed in it. The risk of appropriating `get_pointer` for this is what Peter explained above.

---

## Comment 11

> Username: pdimov  
> Created_at: 2017-05-11 16:47:59 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300849157  

> Well, the comment says "get_pointer(p) extracts a ->* capable pointer from p", so I would say we have the same option with `get_pointer`.  
  
This was the original intent of `get_pointer` - it was a helper function used by `mem_fn` - but since then it's evolved into a generic way to say `p.get()` and is no longer limited to non-NULLs.

---

## Comment 12

> Username: pdimov  
> Created_at: 2017-05-11 16:48:45 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300849358  

I'm still unconvinced on the unsuitability of `addressof(*p)`. Which part of the standard makes this undefined?

---

## Comment 13

> Username: Lastique  
> Created_at: 2017-05-11 17:00:43 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300852598  

> I'm still unconvinced on the unsuitability of addressof(*p). Which part of the standard makes this undefined?  
  
`[unique.ptr.single.observers]/1`, `[util.smartptr.shared.obs]/2`, for example. I'm pretty sure dereferencing raw null pointers was ok, as long as you don't read or write to the resulting reference, but now I can't find a reference in the standard to substantiate this.

---

## Comment 14

> Username: pdimov  
> Created_at: 2017-05-11 17:20:38 UTC  
> Url: https://github.com/boostorg/core/pull/32#issuecomment-300857857  

I mean in the non-NULL, `to_raw_pointer`, case. Here we have a fancy pointer that isn't 0, but that points to allocated raw storage suitable for an object, but with no object there yet.

---
