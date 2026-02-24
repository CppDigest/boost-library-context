# #613 Add reflection option for boundary handling in 1D correlation  [Open]

> Username: meshtag  
> Created at: 2021-06-03 06:33:52 UTC  
> Updated at: 2022-06-04 07:23:04 UTC  
> Url: https://github.com/boostorg/gil/pull/613  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
<!-- What does this pull request do? -->  
  
### References  
Depends on #612   
resolves #608  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-06-03 07:33:15 UTC  
> Updated_at: 2022-06-04 07:23:04 UTC  
> Url: https://github.com/boostorg/gil/pull/613#issuecomment-853648626  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/613?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#613](https://codecov.io/gh/boostorg/gil/pull/613?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1a2a908) into [develop](https://codecov.io/gh/boostorg/gil/commit/843ea374b69dc90b60d289e308c4e35319e8be58?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (843ea37) will **increase** coverage by `0.05%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #613      +/-   ##  
===========================================  
+ Coverage    78.76%   78.82%   +0.05%       
===========================================  
  Files          117      117                
  Lines         5030     5038       +8       
===========================================  
+ Hits          3962     3971       +9       
+ Misses        1068     1067       -1       
```

---

## Review 2 [Changes requested]

> Username: lpranam  
> Created_at: 2021-06-05 07:02:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/613#pullrequestreview-676757369  

Looks good.

📁 test/extension/numeric/test_fixture.hpp

```diff
  30 | 
  31 |+ template <typename SrcView, typename DstView>
  32 |+ void row_conv1D_offset_img_generator(SrcView src_view, DstView dst_view, int const offset,
```

> Username: lpranam  
> Created_at: 2021-06-05 07:02:06 UTC  
> Updated_at: 2021-06-05 07:02:22 UTC  
> Url: https://github.com/boostorg/gil/pull/613#discussion_r645953630  

Good to add comments here about what this function does, else we are gonna have confusion in future the way we did with boundary options.

> Username: meshtag  
> Created_at: 2021-06-05 08:00:34 UTC  
> Url: https://github.com/boostorg/gil/pull/613#discussion_r645958987  

I have added comments, please take a look at them and state whether this comment is resolvable.


---

## Comment 3

> Username: mloskot  
> Created_at: 2022-06-03 06:50:52 UTC  
> Url: https://github.com/boostorg/gil/pull/613#issuecomment-1145644976  

@meshtag Could you tell what is the status of this PR? Is this going to be picked up or drop it and close it?

---
