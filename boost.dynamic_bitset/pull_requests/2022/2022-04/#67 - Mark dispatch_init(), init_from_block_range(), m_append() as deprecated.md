# #67 Mark dispatch_init(), init_from_block_range(), m_append() as deprecated [Closed]

> Username: akr-akari  
> Created at: 2022-04-27 07:45:28 UTC  
> Updated at: 2024-02-08 19:21:57 UTC  
> Closed at: 2024-02-08 19:21:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67  

I found that some member functions such as `dispatch_init`, `init_from_block_range` and `m_append` should not be public, so I moved them to private.  
Thanks!

---

## Review 1 [Changes requested]

> Username: jeking3  
> Created_at: 2022-04-27 11:57:31 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#pullrequestreview-954771393  

While I agree with you in principle, given these are already public, any change to their visibility is a breaking change.  While nothing else inside boost may call them directly, consuming code might).  The proper way to do this would be to deprecate them and get that into release notes so that in a follow-on release one could then actually change their visibility.  This gives consumers a warning they may need to make a change and enough time to reasonably make it.

---

## Comment 2

> Username: akr-akari  
> Created_at: 2022-04-27 13:14:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#issuecomment-1110986729  

> While I agree with you in principle, given these are already public, any change to their visibility is a breaking change. While nothing else inside boost may call them directly, consuming code might). The proper way to do this would be to deprecate them and get that into release notes so that in a follow-on release one could then actually change their visibility. This gives consumers a warning they may need to make a change and enough time to reasonably make it.  
  
Sorry, I'm still not sure how to mark these member functions as deprecated with warnings. Also these member functions are not in the documentation.

---

## Comment 3

> Username: jeking3  
> Created_at: 2022-04-27 13:25:11 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#issuecomment-1110999945  

They may not be in the documentation but they are still public.  This, along with many other boost libraries have been around for a long time and there's no telling how many consuming applications may call one of those, even if they really shouldn't be.  For example someone may have subclassed dynamic_bitset, and `protected` may be more appropriate.  Still, we would need to go through the process of deprecating them I think.  I'm going to ask for some advice from @mclow on properly deprecating public methods so that their visibility can be changed.

---

## Comment 4

> Username: glenfe  
> Created_at: 2022-04-27 13:50:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#issuecomment-1111028404  

- In Boost 1.80.0, announce in documentation and in release notes that these functions will only be provisionally public in Boost 1.81.0 if macros are defined.  
- In Boost 1.81.0 these functions will only be public if some macro like BOOST_DYNAMIC_BITSET_DEPRECATED_PUBLIC is defined. The release notes and documentation will state that in Boost 1.83.0 these functions will be unconditionally private.  
- In Boost 1.83.0 the functions will be unconditionally private.

---

## Comment 5

> Username: jeking3  
> Created_at: 2022-04-27 17:25:28 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#issuecomment-1111281227  

We probably want to change the title of this PR and make an actual issue for this because it will span across multiple releases.

---

## Comment 6

> Username: akr-akari  
> Created_at: 2022-04-27 18:02:30 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#issuecomment-1111318532  

Title has changed.

---

## Comment 7

> Username: jeking3  
> Created_at: 2022-04-27 18:17:41 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#issuecomment-1111333035  

It seems reasonable that dispatch_init was put there to allow subclasses to override or augment the initialization behavior.  Now I'm not so sure it really should be private or move in that direction, maybe protected.  I'm starting to wonder if this shouldn't be discussed on the mailing list before we do anything here.

---

## Comment 8

> Username: akr-akari  
> Created_at: 2022-04-27 18:58:06 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/67#issuecomment-1111369428  

OK, we'll discuss it.

---
