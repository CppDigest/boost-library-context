# #545 Fixed most of warnings in examples and some in core library [Merged]

> Username: Scramjet911  
> Created at: 2021-01-25 05:47:58 UTC  
> Updated at: 2021-02-02 17:53:24 UTC  
> Merged at: 2021-02-02 17:53:16 UTC  
> Closed at: 2021-02-02 17:53:16 UTC  
> Url: https://github.com/boostorg/gil/pull/545  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
This PR fixes multiple warnings showing up during boost build process. Including the fixes of the examples, and some other warnings from core header files.  
  
### References  
#436  
https://github.com/boostorg/gil/blob/81b4dc08bd9177bf9361194a66b0e70cfd6438e8/include/boost/gil/channel.hpp#L651  
Fixing this warning causes a test to fail and I couldn't figure out why that was happening, so I skipped fixing that one for now.  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Fix other warnings  
- [x] Check if all these fixes cause other issues.  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-26 19:02:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/545#pullrequestreview-576667638  

Could you update this PR to get the latest changes from the `develop` branch?

---

## Comment 2

> Username: Scramjet911  
> Created_at: 2021-01-27 11:32:07 UTC  
> Url: https://github.com/boostorg/gil/pull/545#issuecomment-768224508  

> Could you update this PR to get the latest changes from the `develop` branch?  
  
@mloskot Yes, I have done that now, rebased my branch on current develop.

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-28 21:02:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/545#pullrequestreview-578736093  

Thanks for the PR. I suggested a few improvements

📁 example/hessian.cpp

```diff
  82 |             double intensity = 0.0;
  83 |-             for (std::ptrdiff_t filter_y = 0; filter_y < filter_height; ++filter_y)
  83 |+             for (std::ptrdiff_t filter_y = 0; filter_y < static_cast<std::ptrdiff_t>(filter_height); ++filter_y)
```

> Username: mloskot  
> Created_at: 2021-01-28 21:00:21 UTC  
> Updated_at: 2021-01-31 13:37:57 UTC  
> Url: https://github.com/boostorg/gil/pull/545#discussion_r566403489  

I'd rather suggest `constexpr static std::ptrdiff_t filter_height` earlier


📁 include/boost/gil/image_processing/harris.hpp

```diff
  59 |             for (gil::gray32f_view_t::coord_t y_kernel = 0;
  60 |-                 y_kernel < window_length;
  60 |+                 y_kernel < static_cast<std::ptrdiff_t>(window_length);
```

> Username: mloskot  
> Created_at: 2021-01-28 21:01:14 UTC  
> Updated_at: 2021-01-31 13:37:57 UTC  
> Url: https://github.com/boostorg/gil/pull/545#discussion_r566403958  

Again, `auto const window_length` could become `std::ptrdiff_t const window_length`

> Username: Scramjet911  
> Created_at: 2021-01-29 14:11:37 UTC  
> Updated_at: 2021-01-31 13:37:57 UTC  
> Url: https://github.com/boostorg/gil/pull/545#discussion_r566847633  

Ok, did both changes. I rebased it again, but I realized it after pushing that I should have merged.

> Username: mloskot  
> Created_at: 2021-01-29 15:35:34 UTC  
> Updated_at: 2021-01-31 13:37:57 UTC  
> Url: https://github.com/boostorg/gil/pull/545#discussion_r566905197  

@Scramjet911 No worries, rebase is fine too.


---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2021-02-02 17:52:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/545#pullrequestreview-581617260  

LGTM

---
