# #516 Add adaptive histogram equalization algorithm [Merged]

> Username: codejaeger  
> Created at: 2020-08-24 18:03:56 UTC  
> Updated at: 2021-01-24 22:22:49 UTC  
> Merged at: 2021-01-24 22:22:49 UTC  
> Closed at: 2021-01-24 22:22:49 UTC  
> Url: https://github.com/boostorg/gil/pull/516  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Adaptive histogram equalization(AHE) is used in cases when histogram equalization cannot properly contrast enhance due to the presence of both very bright and very dark regions.  
AHE brings out the local information in an image by dividing the image into tiles and then individually performs HE on each of them. Later to remove tiling artefacts  a bi-linear interpolation is performed using mappings from the adjacent tiles. Finally to suppress noise, use contrast clipping to limit the #pixels of a particular value.  
  
This PR adds code, tests & an example to show how to use the GIL AHE algorithm.  
  
### Dependency  
  
This PR requires support of GIL histogram class and the global HE algorithm currently running in PR #499 & PR #514 respectively . Reviewing and merging can only be done post completion of former PRs.  
  
<!-- What does this pull request do? -->  
  
### References  
  
1. [Graphics Gems 4 - Paul S. Heckbert](http://cas.xav.free.fr/Graphics%20Gems%204%20-%20Paul%20S.%20Heckbert.pdf) Pg. 474  
2. [Adaptive Histogram Equalization and its Variations](http://www.cs.unc.edu/Research/Image/MIDAG/pubs/papers/Adaptive%20Histogram%20Equalization%20and%20Its%20Variations.pdf)  
3. Discussion thread on [boost-gil](https://lists.boost.org/boost-gil/2020/08/0454.php) mailing list  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass - RIP Travis CI  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2021-01-24 13:16:06 UTC  
> Url: https://github.com/boostorg/gil/pull/516#issuecomment-766346590  

@codejaeger I've just `git merge`-updated your `gsoc/ahe` branch from the latest `develop`

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2021-01-24 22:22:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/516#pullrequestreview-574996823  

@codejaeger I'm happy to merge this PR. I'm also hoping you'll be available to continue improving the histogram support in GIL. Thank you for your work.

---
