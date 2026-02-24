# #68 Restore waveidl sample to working condition [Merged]

> Username: jefftrull  
> Created at: 2020-02-12 06:13:01 UTC  
> Updated at: 2020-02-12 16:36:41 UTC  
> Merged at: 2020-02-12 14:59:46 UTC  
> Closed at: 2020-02-12 14:59:46 UTC  
> Url: https://github.com/boostorg/wave/pull/68  

- use the idllexer generator instead of the cpp one  
- eliminate a double free encountered during fix  
  
If merged, this will resolve #56

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-12 13:58:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/68#pullrequestreview-357470634  

📁 samples/waveidl/idllexer/idl_re2c_lexer.hpp

```diff
 117 | lexer<IteratorT, PositionT>::~lexer() 
 118 | {
 119 |-     boost::wave::cpplexer::re2clex::aq_terminate(scanner.eol_offsets);
```

> Username: hkaiser  
> Created_at: 2020-02-12 13:58:15 UTC  
> Updated_at: 2020-02-12 13:58:16 UTC  
> Url: https://github.com/boostorg/wave/pull/68#discussion_r378265765  

I don't really remember why `aq_terminate` was in here. Is it not needed anymore?

> Username: jefftrull  
> Created_at: 2020-02-12 14:25:10 UTC  
> Url: https://github.com/boostorg/wave/pull/68#discussion_r378282234  

It is some kind of cleanup code that I don't understand, but what I do know is it already gets called from scanner's destructor :)

> Username: jefftrull  
> Created_at: 2020-02-12 14:25:47 UTC  
> Updated_at: 2020-02-12 14:25:48 UTC  
> Url: https://github.com/boostorg/wave/pull/68#discussion_r378282646  

Also the equivalent call is not present in cpplexer.

> Username: hkaiser  
> Created_at: 2020-02-12 14:59:18 UTC  
> Url: https://github.com/boostorg/wave/pull/68#discussion_r378304677  

Ok, thanks for fixing this!


---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2020-02-12 14:59:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/68#pullrequestreview-357521313  

LGTM, thanks!

---
