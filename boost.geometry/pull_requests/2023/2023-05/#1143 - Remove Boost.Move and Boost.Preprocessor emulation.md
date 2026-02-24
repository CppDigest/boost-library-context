# #1143 [index] Remove Boost.Move and Boost.Preprocessor emulation... [Merged]

> Username: awulkiew  
> Created at: 2023-05-01 20:24:53 UTC  
> Updated at: 2023-06-16 14:03:22 UTC  
> Merged at: 2023-05-16 10:42:48 UTC  
> Closed at: 2023-05-16 10:42:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1143  

...of move semantics and variadic templates

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-05-06 10:53:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1143#pullrequestreview-1415757293  

Looks good to me

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2023-05-08 09:41:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1143#pullrequestreview-1416470623  

LGTM, thanks!

📁 include/boost/geometry/index/detail/varray.hpp

```diff
 646 |     //!   Constant O(1).
 664 |-     void push_back(BOOST_RV_REF(value_type) value)
 647 |+     void push_back(value_type && value)
```

> Username: vissarion  
> Created_at: 2023-05-08 09:36:21 UTC  
> Updated_at: 2023-05-08 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1143#discussion_r1187241474  

I think you should avoid space before `&&` to be consistent. Also in other places in this PR.

> Username: awulkiew  
> Created_at: 2023-05-16 09:43:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1143#discussion_r1194904342  

Yes, I just realized that in code where spaces are placed around operators this looks like logical "and". It's similar with l-value references which looks like bitwise "and" and with pointers that looks like arithmetic "and" ;).

---

📁 include/boost/geometry/index/detail/varray.hpp

```diff
 710 |             value_type & r = *(this->end() - 1);
 728 |-             sv::construct(dti(), this->end(), boost::move(r));                      // may throw
 711 |+             sv::construct(dti(), this->end(), std::move(r));                        // may throw
```

> Username: vissarion  
> Created_at: 2023-05-08 09:37:51 UTC  
> Updated_at: 2023-05-08 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1143#discussion_r1187242801  

This line introduces a trailing whitespace.

> Username: vissarion  
> Created_at: 2023-05-08 09:38:17 UTC  
> Updated_at: 2023-05-08 09:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1143#discussion_r1187243215  

Also happens in other places in this PR.

> Username: awulkiew  
> Created_at: 2023-05-15 22:02:47 UTC  
> Updated_at: 2023-05-15 22:02:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1143#discussion_r1194407407  

This is by design to keep the comments aligned.


---
