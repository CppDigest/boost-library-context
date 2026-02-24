# #115 Prepare for 1.75 release [Merged]

> Username: jefftrull  
> Created at: 2020-10-21 16:48:24 UTC  
> Updated at: 2020-10-21 23:59:34 UTC  
> Merged at: 2020-10-21 23:16:16 UTC  
> Closed at: 2020-10-21 23:16:17 UTC  
> Url: https://github.com/boostorg/wave/pull/115  

"Breaking Changes" deadline is today - seems like a good time for this. Support for C++20 tokens could possibly be classified as "breaking" in the sense that they were lexed differently, before.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2020-10-21 18:04:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/115#pullrequestreview-514039577  

LGTM, thanks for working on this!

---

## Review 2 [Commented]

> Username: hkaiser  
> Created_at: 2020-10-21 18:06:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/115#pullrequestreview-514042161  

There is also `operator<=>()` that might need special handling, but that can be added later. Also what about attributes (not exactly C++20, but anyways)?

---

## Comment 3

> Username: jefftrull  
> Created_at: 2020-10-21 23:03:35 UTC  
> Url: https://github.com/boostorg/wave/pull/115#issuecomment-713935105  

Oh man, haven't given any thought to attributes... or spaceship... guess I'll file some more issues.

---

## Comment 4

> Username: jefftrull  
> Created_at: 2020-10-21 23:16:02 UTC  
> Url: https://github.com/boostorg/wave/pull/115#issuecomment-713955575  

Re: attributes, is `__has_cpp_attribute()` what you were referring to? I haven't gotten any further than filing an issue for it.

---

## Comment 5

> Username: hkaiser  
> Created_at: 2020-10-21 23:20:01 UTC  
> Url: https://github.com/boostorg/wave/pull/115#issuecomment-713962003  

> Re: attributes, is `__has_cpp_attribute()` what you were referring to? I haven't gotten any further than filing an issue for it.  
  
I meant `[[some_attribute]]` which are separate special tokens, like `T_ATTRIBUTE` which has a value of `[[some_attribute]]`.

---

## Comment 6

> Username: jefftrull  
> Created_at: 2020-10-21 23:41:10 UTC  
> Url: https://github.com/boostorg/wave/pull/115#issuecomment-713994812  

Ah... FWIW Clang doesn't seem to have a special token for that. Is there a specific need, or can we let the parser handle that?

---

## Comment 7

> Username: hkaiser  
> Created_at: 2020-10-21 23:59:34 UTC  
> Url: https://github.com/boostorg/wave/pull/115#issuecomment-714020263  

> Ah... FWIW Clang doesn't seem to have a special token for that. Is there a specific need, or can we let the parser handle that?  
  
Ok. Could be a convenience only. However it could be handled by the parser as a sequence of `[` `[` `identifier` `]` `]` tokens...

---
