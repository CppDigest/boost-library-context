# #708 fix: Wrong return type of copy_fn and missing typedef in destruct_range [Open]

> Username: marco-langer  
> Created at: 2022-07-05 15:56:24 UTC  
> Updated at: 2024-08-20 13:02:30 UTC  
> Url: https://github.com/boostorg/gil/pull/708  

### Description  
  
This PR fixes some minor issues in algorithm.hpp:  
  
- removed unnecessary non-const std::copy overload  
- fixed wrong return type of copy_fn  
- added missing typedef to destruct_range   
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-07-05 16:06:28 UTC  
> Url: https://github.com/boostorg/gil/pull/708#issuecomment-1175232320  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/708?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#708](https://codecov.io/gh/boostorg/gil/pull/708?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7e87ecd) into [develop](https://codecov.io/gh/boostorg/gil/commit/573ba132cd182f0bde819d1f1abcd13adcf00623?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (573ba13) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #708      +/-   ##  
===========================================  
- Coverage    81.10%   81.10%   -0.01%       
===========================================  
  Files          117      117                
  Lines         5171     5170       -1       
===========================================  
- Hits          4194     4193       -1       
  Misses         977      977                
```

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2022-07-05 20:02:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/708#pullrequestreview-1029146345  

LGTM, thanks!  
  
Is this still a draft?

📁 include/boost/gil/algorithm.hpp

```diff
 162 | template <typename I, typename O> struct copy_fn {
 169 |-     BOOST_FORCEINLINE I operator()(I first, I last, O dst) const { return std::copy(first,last,dst); }
 163 |+     BOOST_FORCEINLINE O operator()(I first, I last, O dst) const { return std::copy(first,last,dst); }
```

> Username: mloskot  
> Created_at: 2022-07-05 20:02:19 UTC  
> Updated_at: 2022-07-05 20:02:29 UTC  
> Url: https://github.com/boostorg/gil/pull/708#discussion_r914160193  

Hmm, amazing it remained unrevealed for long time.  
How did you find it out?

> Username: marco-langer  
> Created_at: 2022-07-06 05:48:34 UTC  
> Url: https://github.com/boostorg/gil/pull/708#discussion_r914437662  

> Hmm, amazing it remained unrevealed for long time.  
  
I was also surprised after realizing this bug has been there for at least 15 years. I guess the reason is copy_fn is not used in many places and as long as the output iterator is implicitly convertible to the input iterator (as it is the case for pointers which only differ in their const-qualifier), everything is fine.  
  
> How did you find it out?  
  
Just me, the algorithm header and a cup of coffee.


---

## Comment 3

> Username: marco-langer  
> Created_at: 2022-07-06 06:03:18 UTC  
> Updated_at: 2022-07-06 06:44:19 UTC  
> Url: https://github.com/boostorg/gil/pull/708#issuecomment-1175815858  

> Is this still a draft?  
  
I wanted to think a second time about these core library changes before rushing them as a last minute change into the next release.  
  
- Why didn't the missing typedef in `destruct_range` show up in the CI? Is this dead code? Should we add more test cases? Is this part, which deals with destructing non-trivial-destructible pixel types, some kind of academic over-engineering which has no real world application (otherwise there would have been already bug reports?)?  
- Are those two std::copy specializations really redundant? And if yes, is specialization of std::copy for pixel types necessary at all? Shouldn't std::copy internally already employ a memmove strategy in case of trivial copyable value types? Wouldn't this be a better solution rather than this near-UB reinterpret_cast of the pixel types? Are the pixel types trivial copyable? If not, why not?

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-07-06 08:55:20 UTC  
> Url: https://github.com/boostorg/gil/pull/708#issuecomment-1175963622  

I agree with your points @marco-langer   
I also was wondering about the actual redundancy of the `std::copy` thing.  
Let's postpone it after 1.80 is out - it's already too late for major changes (other than tweaking internal stuff like CI or CMake configs).

---
