# #173 Fix/assignment operators for iterators [Merged]

> Username: mkaravel  
> Created at: 2014-11-03 14:24:40 UTC  
> Updated at: 2014-11-05 06:29:06 UTC  
> Merged at: 2014-11-03 19:11:39 UTC  
> Closed at: 2014-11-03 19:11:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/173  

Fix assignment operator implementations for iterators:  
- Remove templated assignment operators as they are not really needed (the corresponding functionality is provided by the explicitly or implicitly defined non-templated assignment operators, combined with templated constructors). Applies to `range_segment_iterator`, `concatenate_iterator` and `flatten_iterator`.  
- Remove assignment operators when they are the same as those that would have been created automatically by the compiler. Applies to `closing_iterator` and `ever_circling_iterator`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-11-03 17:16:07 UTC  
> Updated_at: 2014-11-03 19:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/173#discussion_r19748114  

Hey, these ======= are a creative separation ;-)  
Please put the second after == and indent the ==

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-11-03 17:19:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/173#issuecomment-61513108  

OK, thanks, I'm OK with merging (after changing that (IMO) unusual indentation)

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-11-03 19:10:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/173#discussion_r19756448  

Done. Hope you like the updated statement layout better.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-11-03 19:11:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/173#issuecomment-61530686  

Should be ready for merging, assuming that the new statement layout is acceptable.

---
