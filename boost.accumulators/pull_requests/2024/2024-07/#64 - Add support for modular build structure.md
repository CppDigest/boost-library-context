# #64 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:29:36 UTC  
> Updated at: 2025-06-26 14:24:29 UTC  
> Merged at: 2025-06-26 14:24:29 UTC  
> Closed at: 2025-06-26 14:24:29 UTC  
> Url: https://github.com/boostorg/accumulators/pull/64  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:00:26 UTC  
> Url: https://github.com/boostorg/accumulators/pull/64#issuecomment-2295310321  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-05-02 16:03:40 UTC  
> Url: https://github.com/boostorg/accumulators/pull/64#issuecomment-2847586126  

This changes CI to use cpp-actions, which has nothing to do with modularity and should be a separate PR (which I'm not going to merge.)

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2025-05-02 16:17:02 UTC  
> Url: https://github.com/boostorg/accumulators/pull/64#issuecomment-2847612860  

It was the only way I could get the tests to pass. If you are willing to merge this without the tests passing I can undo the CI changes.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-06-06 23:46:36 UTC  
> Url: https://github.com/boostorg/accumulators/pull/64#issuecomment-2951235218  

Why the change from `toolset: gcc-4.8` to `toolset: gcc; compiler: g++-4.8`? The former should work.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2025-06-07 00:17:50 UTC  
> Url: https://github.com/boostorg/accumulators/pull/64#issuecomment-2951279230  

> Why the change from `toolset: gcc-4.8` to `toolset: gcc; compiler: g++-4.8`? The former should work.  
  
IIRC one of the runs like that didn't actually work. So switched to being explicit.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2025-06-07 00:20:11 UTC  
> Url: https://github.com/boostorg/accumulators/pull/64#issuecomment-2951283597  

PS. Technically it should not work. As that toolset ends up in user-config. And that should use the plain toolset names to import. The versioned gcc-x.y only works in the CLI feature spec which gets parsed and auto-initialized as given.

---
