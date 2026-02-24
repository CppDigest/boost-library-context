# #267 Add define BOOST_PARSER_DISABLE_TRACE to disable trace mode at compile time [Closed]

> Username: andreasbuhr  
> Created at: 2025-08-07 12:11:35 UTC  
> Updated at: 2026-02-14 19:48:41 UTC  
> Closed at: 2026-02-14 19:48:41 UTC  
> Url: https://github.com/boostorg/parser/pull/267  

The trace feature doubles the compile time, even if never used. This patch introduces the preprocessor define BOOST_PARSER_DISABLE_TRACE to deactivate this feature at compile time.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-11-01 16:12:31 UTC  
> Url: https://github.com/boostorg/parser/pull/267#issuecomment-3476501921  

I'd be much happier merging this if it had a proper place in config.hpp, and had associated documentation.  I think this is reasonable, so I'll do it eventually, but it's pretty low priority for me.

---

## Comment 2

> Username: andreasbuhr  
> Created_at: 2025-11-01 17:02:35 UTC  
> Url: https://github.com/boostorg/parser/pull/267#issuecomment-3476560342  

Great. I'll improve this Pull Request over time and rebase it on develop from time to time. Expect it to be polished in mid of January.

---

## Comment 3

> Username: andreasbuhr  
> Created_at: 2026-02-03 10:33:54 UTC  
> Url: https://github.com/boostorg/parser/pull/267#issuecomment-3840496569  

Rebased on develop.  
This is ready to be merged from my perspective. If you have suggestions to improve this, let me know.

---

## Comment 4

> Username: tzlaine  
> Created_at: 2026-02-14 19:30:20 UTC  
> Updated_at: 2026-02-14 19:48:38 UTC  
> Url: https://github.com/boostorg/parser/pull/267#issuecomment-3902391629  

I'm not going to commit this as-is, because of nits about the way you wrote the change; the semantics seem fine.  It was quicker to just change it to my liking than to describe what I wanted changed.  See your modified commit on the https://github.com/boostorg/parser/tree/introduce_define_disable_trace branch if you're curious.  The tl;dr is I introduced a "private"/undocumented macro that I hang everything off of, and define it based on whether the user defines the user-facing one.  This is the pattern for all macros in all my Boost libs.  One advantage of doing this is that you can treat the macro as a constant, and don't need `#if`s in as many places, which IMO improves readability.  
  
Superceded by PR #314 .

---
