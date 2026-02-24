# #638 Renamed adaptive_he to adaptive_histogram_equalization [Merged]

> Username: Paul92  
> Created at: 2022-03-17 13:50:03 UTC  
> Updated at: 2022-04-21 14:38:33 UTC  
> Merged at: 2022-04-21 14:38:19 UTC  
> Closed at: 2022-04-21 14:38:19 UTC  
> Url: https://github.com/boostorg/gil/pull/638  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
On develop, tests were failing because of missing `adaptive_he.cpp`, which was renamed to `adaptive_histogram_equalization.cpp`.  
Since `adaptive_histogram_equalization` seems more consistent, I took the liberty to modify all the occurences of `adaptive_he` to `adaptive_histogram_equalization.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: lpranam  
> Created_at: 2022-03-17 14:29:34 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/638#pullrequestreview-913207850  

Thanks for the PR. You changed the source file name in jamfile but forgot to change the name of the actual file :)

---

## Comment 2

> Username: Paul92  
> Created_at: 2022-03-17 18:54:46 UTC  
> Updated_at: 2022-03-17 18:55:05 UTC  
> Url: https://github.com/boostorg/gil/pull/638#issuecomment-1071214765  

Thanks for your comment. It's either me being confused or not making myself clear, but the problem was that the tests weren't working since `adaptive_histogram_equalization.cpp` was already renamed (hence the tests not working) - I just renamed the rest.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2022-03-18 16:04:41 UTC  
> Url: https://github.com/boostorg/gil/pull/638#issuecomment-1072555206  

Maybe the confusion comes from the fact that this MR also renames the documentation `.md` file for consistency (which is fine).

---

## Comment 4

> Username: sdebionne  
> Created_at: 2022-03-22 10:41:59 UTC  
> Url: https://github.com/boostorg/gil/pull/638#issuecomment-1075010684  

I believe the failing pipelines have nothing to do with the changes introduced in this PR:  
  
```  
testing.capture-output bin.v2/libs/gil/test/extension/io/simple_all_formats.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden/simple_all_formats.run  
====== BEGIN OUTPUT ======  
Segmentation fault (core dumped)  
  
EXIT STATUS: 139  
====== END OUTPUT ======  
```  
  
The runners are probably running out of memory  (or `cxxstd-2a-iso` is buggy in GCC8).

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-03-22 11:31:09 UTC  
> Url: https://github.com/boostorg/gil/pull/638#issuecomment-1075061557  

@sdebionne Yes, I think this can be merged.  
  
It's shame the CI is still broken, but my work here https://github.com/mloskot/gil/pull/3 is still being kind of blocked by https://github.com/boostorg/filesystem/issues/226 aka https://github.com/boostorg/cmake/issues/17  
  
Although with this https://github.com/boostorg/gil/pull/636 merged, it should be easier to work around it. I'll pick the task this week yet.

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2022-04-21 14:38:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/638#pullrequestreview-948733165  

Thanks. I'm going to merge this.  
  
If it causes any CI failures, then I'll take care of them.

---
