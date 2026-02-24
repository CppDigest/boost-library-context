# #831 Fix Clang-15 warnings [Merged]

> Username: SiliconA-Z  
> Created at: 2022-10-04 16:56:28 UTC  
> Updated at: 2022-10-25 15:38:16 UTC  
> Merged at: 2022-10-09 18:18:24 UTC  
> Closed at: 2022-10-09 18:18:24 UTC  
> Url: https://github.com/boostorg/math/pull/831  

Redundant casts, noexcept for move constructors, and usage of default and delete make the code more performant and maintainable

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-10-04 17:56:51 UTC  
> Updated_at: 2022-10-04 17:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/831#issuecomment-1267358703  

Hi @AtariDreams  
  
Thanks for diving into this.  
I did a quick appraisal of the results of the drone run in CI (which runs even without admin approval).  
  
To my rather quick glance it seems like a lot of tests fail with these changes. I'm not sure exactly what went wrong, but I believe this should be investigated prior to running all of CI.  
  
Cc: @jzmaddock and @NAThompson and @mborland

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-10-04 18:17:34 UTC  
> Url: https://github.com/boostorg/math/pull/831#issuecomment-1267379377  

There's a stray trailing "," in stats.hpp.

---

## Comment 3

> Username: SiliconA-Z  
> Created_at: 2022-10-05 13:17:13 UTC  
> Url: https://github.com/boostorg/math/pull/831#issuecomment-1268422646  

Fixed!

---

## Comment 4

> Username: SiliconA-Z  
> Created_at: 2022-10-05 16:27:14 UTC  
> Url: https://github.com/boostorg/math/pull/831#issuecomment-1268660410  

@ckormanyos

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-10-06 10:36:59 UTC  
> Url: https://github.com/boostorg/math/pull/831#issuecomment-1269780114  

Hi @AtariDreams thanks for correcting these suggested corrections.  
  
Could we wait a few hours or half a day or so before approving CI? John (@jzmaddock) has been working steadily and dilligently on some special functions correctins and these are cacling now.  
  
I (or somebody) will approve this run after some of heavy action finishes up, which should be in a few hours.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2022-10-06 10:38:59 UTC  
> Url: https://github.com/boostorg/math/pull/831#issuecomment-1269782801  

To be more clear @AtariDreams to me it would seem nice to get #835 (cycling in CI now) merged (see work by @jzmaddock). Then merge that updated develop into your branch and then run all of CI.  
  
Thoughts?

---

## Comment 7

> Username: SiliconA-Z  
> Created_at: 2022-10-09 17:24:25 UTC  
> Url: https://github.com/boostorg/math/pull/831#issuecomment-1272589304  

That makes sense. Perfect!

---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-09 18:14:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/831#pullrequestreview-1135372847  

📁 include/boost/math/tools/polynomial_gcd.hpp

```diff
  24 | #include <utility>
  25 | #include <iterator>
  26 |- #include <type_traits>
```

> Username: jzmaddock  
> Created_at: 2022-10-09 18:14:17 UTC  
> Url: https://github.com/boostorg/math/pull/831#discussion_r990821078  

This header use std::is_arithmetic: surely we should keep this #include?

> Username: jzmaddock  
> Created_at: 2022-10-09 18:18:01 UTC  
> Url: https://github.com/boostorg/math/pull/831#discussion_r990821450  

Ah no I see, yes you're quite right!


---
