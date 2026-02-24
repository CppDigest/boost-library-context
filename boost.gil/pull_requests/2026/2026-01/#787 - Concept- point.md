# #787 Concept: point [Open]

> Username: sdebionne  
> Created at: 2026-01-23 11:47:17 UTC  
> Updated at: 2026-01-27 13:39:04 UTC  
> Url: https://github.com/boostorg/gil/pull/787  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Switch `Point` concept from Boost.ConceptCheck to C++20 concepts.  
  
### References  
  
Discussed on `boost-gil` cpplang  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Adapt test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2026-01-23 11:51:20 UTC  
> Url: https://github.com/boostorg/gil/pull/787#issuecomment-3789892679  

I am not sure what to do with the CI. Obviously all builds with cxxstd < 20 will fail.

---

## Review 2 [Commented]

> Username: sdebionne  
> Created_at: 2026-01-23 12:17:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/787#pullrequestreview-3697223358  

📁 include/boost/gil/concepts/point.hpp

```diff
  58 | template <typename P>
  61 |- struct PointNDConcept
  59 |+ concept PointNDConcept = requires(P point, typename P::template axis<0>::coord_t ft, typename P::template axis<P::num_dimensions - 1>::coord_t lt)
```

> Username: sdebionne  
> Created_at: 2026-01-23 12:14:56 UTC  
> Updated_at: 2026-01-23 12:17:45 UTC  
> Url: https://github.com/boostorg/gil/pull/787#discussion_r2720966974  

concept  should be lower case?

---

📁 include/boost/gil/concepts/point.hpp

```diff
  82 |-     P point;
  61 |+     typename P::value_type;
  62 |+     requires std::regular<typename P::value_type>;
```

> Username: sdebionne  
> Created_at: 2026-01-23 12:16:41 UTC  
> Updated_at: 2026-01-23 12:17:45 UTC  
> Url: https://github.com/boostorg/gil/pull/787#discussion_r2720972165  

Better write with concept conjunctions?  
```  
concept PointNDConcept = std::regular<typename P::value_type> && requires(P point, typename...  
```


---

## Comment 3

> Username: mloskot  
> Created_at: 2026-01-23 17:07:31 UTC  
> Url: https://github.com/boostorg/gil/pull/787#issuecomment-3791308447  

> Obviously all builds with cxxstd < 20 will fail.  
  
Well, any build other than c++20 will fail, no?  
  
Since GIL cannot afford the luxury of https://github.com/boostorg/cobalt, I don't know any tricks other than the old-school  
https://github.com/boostorg/url/blob/f4a80080b1012a0f0a7fb6cd472ff662fa8daefe/include/boost/url/grammar/charset.hpp#L69

---

## Comment 4

> Username: sdebionne  
> Created_at: 2026-01-24 15:33:25 UTC  
> Url: https://github.com/boostorg/gil/pull/787#issuecomment-3794820685  

Right, another option would be GIL v2.

---

## Comment 5

> Username: mloskot  
> Created_at: 2026-01-24 17:51:33 UTC  
> Url: https://github.com/boostorg/gil/pull/787#issuecomment-3795259502  

If we would be allowed to consider GIL up to Boost 1.90 as last GIL that supports mixture of older and newer compilers, and any GIL released after Boost 1.90 require >= C++20, that would be great! It would be an opportunity for more thorough refactoring and better experience for implementing new features.

---

## Comment 6

> Username: sdebionne  
> Created_at: 2026-01-26 08:21:16 UTC  
> Url: https://github.com/boostorg/gil/pull/787#issuecomment-3798374730  

> If we would be allowed to consider...  
  
Is it our decision or shall we ask the community or a boost comity for advise? Would that involve a review process?  
  
I fully agree that much of the code of the library could be re-written in a simpler way with the new language feature (e.g. one-liner lambda instead of function object).  
  
For me, getting a meaningful compiler error message when using GIL instead of a gigantic stack of errors would be a huge improvement. Maybe I am wrong but I hope that the switch to C++20 concepts should help.  
  
In the meantime, I can try to keep the compatibility to see what that involves in term of added complexity and boilerplate.

---

## Comment 7

> Username: mloskot  
> Created_at: 2026-01-26 11:49:52 UTC  
> Url: https://github.com/boostorg/gil/pull/787#issuecomment-3799177405  

> Is it our decision or shall we ask the community or a boost comity for advise? Would that involve a review process?  
  
@sdebionne Good questions. [Trying to learn about it on the Slack](https://cpplang.slack.com/archives/C27KZLB0X/p1769427521873789).

---
