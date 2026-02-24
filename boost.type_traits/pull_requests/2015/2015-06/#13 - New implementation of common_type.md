# #13  New implementation of common_type. [Merged]

> Username: pdimov  
> Created at: 2015-06-07 16:28:09 UTC  
> Updated at: 2015-06-08 11:04:41 UTC  
> Merged at: 2015-06-08 08:18:28 UTC  
> Closed at: 2015-06-08 08:18:28 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-06-07 17:12:22 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13#issuecomment-109776790  

Thanks Peter, I much appreciate this.  
  
Two quick questions:  
- Can we have some docs for copy_cv (this could be a later PR actually if that's easier).  
- I wonder about the naming of tp_identity.  Just "identity" would be much better, but I guess that's an issue directly in the boost namespace?  Whatever, tp_identity is horrible IMO, and "type_identity" is only slightly better.  Maybe through this one open to the list?

---

## Comment 2

> Username: pdimov  
> Created_at: 2015-06-07 17:21:13 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13#issuecomment-109778410  

`identity` is traditionally taken by the function object, which is why I didn't want to use this name. `type_identity` doesn't look horrible to me, but I don't care much one way or the other. It could even go to `type_traits_detail`, but `conditional` isn't very useful without a type identity. Up to you.  
  
I also wanted to suggest moving `declval.hpp` to `type_traits` as that's where it belongs, but didn't want to clutter this PR. `declval` is also missing from the docs.  
  
A SFINAE-friendly common_type is next on my list, but let's get this over first. I also want to add a few more tests. Do I have to follow the current `"test.hpp"` style, or can I use `lightweight_test_trait` instead?

---

## Comment 3

> Username: pdimov  
> Created_at: 2015-06-07 18:25:47 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13#issuecomment-109785991  

Let me know what you want done with `tp_identity` and I'll update the PR.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-06-07 18:43:29 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13#issuecomment-109787790  

> |identity| is traditionally taken by the function object, which is why   
> I didn't want to use this name. |type_identity| doesn't look horrible   
> to me, but I don't care much one way or the other. It could even go to   
> |type_traits_detail|, but |conditional| isn't very useful without a   
> type identity. Up to you.  
>   
> OK, lets try type_identity then.  
  
BTW I don't see the use case with conditional - not unless it were lazy?  
  
> I also wanted to suggest moving |declval.hpp| to |type_traits| as   
> that's where it belongs, but didn't want to clutter this PR. |declval|   
> is also missing from the docs.  
>   
> +1.  
  
I'm working on is_constructible/is_copy_assignable and friends which are   
needed to fix bugs in the has_nothrow_\* traits, and all need decltype.    
Also declref and decl_const_ref are useful too for that matter...  
  
John.

---

## Comment 5

> Username: pdimov  
> Created_at: 2015-06-07 19:21:42 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13#issuecomment-109789841  

Renamed to `type_identity`.

---

## Comment 6

> Username: pdimov  
> Created_at: 2015-06-07 23:10:17 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13#issuecomment-109812767  

`declref` is just `declval<T&>()`.  
  
The use case of `type_identity` with `conditional` is  
  
```  
template<class T, class U> struct common_type_class: public boost::conditional<  
  
    boost::is_convertible<T, U>::value && !boost::is_convertible<U, T>::value,  
    boost::type_identity<U>,  
  
    typename boost::conditional<  
  
        boost::is_convertible<U, T>::value && !boost::is_convertible<T, U>::value,  
        boost::type_identity<T>,  
  
        common_type_impl3<T, U>  
    >::type  
>::type  
{  
};  
```  
  
It's possible to move the `conditional` inside a `type` `typedef` and avoid the identities, but then if `common_type_impl3` is SFINAE-friendly (which it here isn't, but in principle), the result won't be.

---

## Comment 7

> Username: pdimov  
> Created_at: 2015-06-07 23:12:22 UTC  
> Url: https://github.com/boostorg/type_traits/pull/13#issuecomment-109812851  

I'm done for today. Feel free to merge if everything checks out.

---
