# #151 Change base of tuple [Merged]

> Username: Flast  
> Created at: 2017-10-12 14:55:58 UTC  
> Updated at: 2017-10-13 16:14:49 UTC  
> Merged at: 2017-10-12 14:56:24 UTC  
> Closed at: 2017-10-12 14:56:24 UTC  
> Url: https://github.com/boostorg/fusion/pull/151  

As discussed on [1], change base class of `fusion::tuple` to `vector_detail::vector_data` to improve compile time performance and conversion stability.  
  
Tested:  
- GCC 7.2.1 20170829  
- Clang 4.0.1  
  
[1] [[Spirit-general] [fusion] Proposal for deprecating fusion.tuple.](https://sourceforge.net/p/spirit/mailman/spirit-general/thread/ed8d507e-f79d-6710-644e-8b11417f7a2c@flast.jp/#msg36056640)

---

## Review 1 [Commented]

> Username: vtnerd  
> Created_at: 2017-10-13 15:14:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/151#pullrequestreview-69260962  

📁 include/boost/fusion/container/vector/vector.hpp

```diff
 309 |-           , typename Sequence_ = typename remove_reference<Sequence>::type
 310 |-           , typename = typename boost::enable_if_c<(
 311 |-                 !is_base_of<vector, Sequence_>::value &&
```

> Username: vtnerd  
> Created_at: 2017-10-13 15:14:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/151#discussion_r144581875  

This check was added to speedup the compile performance of copies/moves. This constructor is now used for copies/moves, and has to instantiate an `at_c<...>(...)` for each element instead of using the compiler generated default. I didn't add a comment or store this information anywhere, so maybe am I mistaken.  
  
I will try to investigate this again and update with a comment + PR. I commented in this PR in case I forget to do it, someone else might want to know.

> Username: Flast  
> Created_at: 2017-10-13 16:12:15 UTC  
> Updated_at: 2017-10-13 16:14:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/151#discussion_r144596457  

If so, (perhaps) using `is_same` is better than `is_base_of`.


---
