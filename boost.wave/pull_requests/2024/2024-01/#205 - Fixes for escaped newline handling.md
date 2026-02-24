# #205 Fixes for escaped newline handling [Merged]

> Username: jefftrull  
> Created at: 2024-01-29 00:05:32 UTC  
> Updated at: 2024-01-30 00:57:04 UTC  
> Merged at: 2024-01-30 00:57:04 UTC  
> Closed at: 2024-01-30 00:57:04 UTC  
> Url: https://github.com/boostorg/wave/pull/205  

Experiments with `BOOST_WAVE_BSIZE` revealed that we were missing some backslash+newline cases, and in others, testing invalid memory locations for the presence of a newline.  
  
These fixes repair the specific issues found but a more general solution is probably in order (and will be particularly needed given the more lenient treatment of newline escapes in C++23)

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2024-01-29 00:57:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/205#pullrequestreview-1847683316  

Could you add some somment to the code you changed for us to be able to remember what's happening?

📁 include/boost/wave/cpplexer/re2clex/cpp_re.hpp

```diff
 237 |         /* first scan for backslash-newline and erase them */
 238 |-         for (p = s->lim; p < s->lim + cnt - 2; ++p)
 238 |+         for (p = std::max(s->lim - 3, s->cur); p < s->lim + cnt - 2; ++p)
```

> Username: hkaiser  
> Created_at: 2024-01-29 00:56:15 UTC  
> Updated_at: 2024-01-29 00:57:04 UTC  
> Url: https://github.com/boostorg/wave/pull/205#discussion_r1468981202  

```diff  
-        for (p = std::max(s->lim - 3, s->cur); p < s->lim + cnt - 2; ++p)  
+        for (p = (std::max)(s->lim - 3, s->cur); p < s->lim + cnt - 2; ++p)  
```


---

## Comment 2

> Username: jefftrull  
> Created_at: 2024-01-29 02:22:39 UTC  
> Url: https://github.com/boostorg/wave/pull/205#issuecomment-1913852737  

Thanks for the feedback; LMK what you think.

---

## Review 3 [Approved]

> Username: hkaiser  
> Created_at: 2024-01-29 11:58:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/205#pullrequestreview-1848502469  

LGTM, thanks!

---
