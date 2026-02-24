# #16 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:24:18 UTC  
> Updated at: 2025-06-26 16:42:29 UTC  
> Merged at: 2025-06-26 16:42:29 UTC  
> Closed at: 2025-06-26 16:42:29 UTC  
> Url: https://github.com/boostorg/io/pull/16  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:53:53 UTC  
> Url: https://github.com/boostorg/io/pull/16#issuecomment-2295308696  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-06-25 23:53:59 UTC  
> Url: https://github.com/boostorg/io/pull/16#issuecomment-3006561379  

I don't think the changes from `toolset: gcc-x` to `toolset: gcc; compiler: g++-x` are necessary.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-06-26 12:37:54 UTC  
> Url: https://github.com/boostorg/io/pull/16#issuecomment-3008346072  

The CI here used both `standard` and `cxxstd` with only the former working :-)  
  
Unless @glenfe has an opinion, I suspect it will be less error-prone to switch to `cxxstd` because that's what everyone else uses and it will inevitably get back here via copy-paste.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-06-26 14:04:56 UTC  
> Url: https://github.com/boostorg/io/pull/16#issuecomment-3008621985  

Same here, it's ready, except for Appveyor.

---
