# #514 Add histogram equalization feature [Merged]

> Username: codejaeger  
> Created at: 2020-08-24 14:21:15 UTC  
> Updated at: 2021-01-25 14:53:28 UTC  
> Merged at: 2021-01-24 20:32:40 UTC  
> Closed at: 2021-01-24 20:32:40 UTC  
> Url: https://github.com/boostorg/gil/pull/514  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Histogram equalization is an important contrast enhancement algorithm. It is an application of histograms to contrast stretch images.  
  
<!-- What does this pull request do? -->  
  
Contains code, tests, example and doc for the algorithm global histogram equalization started during GSOC'2020.  
The code has been tested on variety of standard test images to find any noticeable errors. A comparison with implementations from other image processing libraries has also been made.  
  
### Dependency  
  
This PR requires support of GIL histogram class currently running in PR #499 . Reviewing and merging can only be done post completion of former PRs.  
  
### References  
  
Wiki - https://en.wikipedia.org/wiki/Histogram_equalization  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
Related PRs - #435, #442  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass - R.I.P. Travis CI  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2021-01-24 13:13:45 UTC  
> Url: https://github.com/boostorg/gil/pull/514#issuecomment-766346228  

@codejaeger I've just `git merge`-updated your `gsoc/histogram_equalization` branch from the latest `develop`

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2021-01-24 20:32:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/514#pullrequestreview-574966375  

@codejaeger I'm happy to merge this PR. I'm also hoping you'll be available to continue improving the histogram support in GIL. Thank you for your work.

---

## Comment 3

> Username: codejaeger  
> Created_at: 2021-01-25 14:53:27 UTC  
> Url: https://github.com/boostorg/gil/pull/514#issuecomment-766870952  

> @codejaeger I'm happy to merge this PR. I'm also hoping you'll be available to continue improving the histogram support in GIL. Thank you for your work.  
  
@mloskot Yes currently I am aiming to improve the version of contrast limited AHE implemented to a faster version. I can only see general improvements post that for the histogram class in general (like improving the docs and making the interface simpler to use as @lpranam had suggested).

---
