# #66 Ensure wave driver and sample programs build in CI, fixing issues turned up during the process. [Merged]

> Username: jefftrull  
> Created at: 2020-02-11 05:26:33 UTC  
> Updated at: 2020-02-11 19:06:59 UTC  
> Merged at: 2020-02-11 19:06:59 UTC  
> Closed at: 2020-02-11 19:06:59 UTC  
> Url: https://github.com/boostorg/wave/pull/66  

Display a Travis build status badge and some minimal description in a README  
  
If merged this will resolve #48.

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-11 12:19:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/66#pullrequestreview-356611717  

📁 samples/token_statistics/build/Jamfile.v2

```diff
  21 |         /boost/wave//boost_wave
  22 |-         /boost/program_options//boost_program_options
  22 |+         /boost/program_options//boost_program_options/<link>static
```

> Username: hkaiser  
> Created_at: 2020-02-11 12:19:39 UTC  
> Url: https://github.com/boostorg/wave/pull/66#discussion_r377600172  

What's the rationale of this change? Could you elaborate, please?

> Username: jefftrull  
> Created_at: 2020-02-11 17:11:22 UTC  
> Updated_at: 2020-02-11 17:11:50 UTC  
> Url: https://github.com/boostorg/wave/pull/66#discussion_r377775573  

Sure! This stems from a problem linking on MSVC-12. A nearly identical issue was reported at https://github.com/boostorg/program_options/issues/58; after investigating it for a while I realized that the tactic used within Wave [here](https://github.com/boostorg/wave/blob/18f834ac355d65d18619af673122c23623e7c912/test/build/Jamfile.v2#L93) would fix this as well.  
  
I did also try adding `BOOST_ALL_DYN_LINK` but then it seemed like some inappropriate things got marked `__dllimport`, in particular `expression_grammar_gen<TokenT>::evaluate` and MSVC didn't like that either.  
  
I haven't tried using `BOOST_PROGRAM_OPTIONS_DYN_LINK` but could attempt it if you prefer that approach.

> Username: hkaiser  
> Created_at: 2020-02-11 19:05:16 UTC  
> Updated_at: 2020-02-11 19:05:17 UTC  
> Url: https://github.com/boostorg/wave/pull/66#discussion_r377837444  

Don't worry about this too much, I was just curious.


---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2020-02-11 19:05:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/66#pullrequestreview-356916506  

LGTM, thanks!

---
