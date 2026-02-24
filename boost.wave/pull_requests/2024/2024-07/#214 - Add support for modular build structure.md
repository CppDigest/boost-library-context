# #214 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:28:17 UTC  
> Updated at: 2024-08-27 05:01:03 UTC  
> Merged at: 2024-08-27 05:01:03 UTC  
> Closed at: 2024-08-27 05:01:03 UTC  
> Url: https://github.com/boostorg/wave/pull/214  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:58:46 UTC  
> Url: https://github.com/boostorg/wave/pull/214#issuecomment-2295309908  

Please review and merge this PR at your earliest convenience.

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2024-08-18 16:12:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/214#pullrequestreview-2244351770  

LGTM, thanks!

---

## Review 3 [Commented]

> Username: jefftrull  
> Created_at: 2024-08-24 12:11:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/214#pullrequestreview-2258683724  

📁 test/build/Jamfile.v2

```diff
  71 |         cxx11_hdr_regex
  72 |       ]
  73 |+       <include>$(SAMPLES_DIR)
```

> Username: jefftrull  
> Created_at: 2024-08-24 12:11:14 UTC  
> Updated_at: 2024-08-24 12:11:15 UTC  
> Url: https://github.com/boostorg/wave/pull/214#discussion_r1729950199  

I'm surprised to see that the tests might depend on the samples (and that this was detected in the process of this work). Can you recall why this is required?

> Username: jefftrull  
> Created_at: 2024-08-24 12:17:01 UTC  
> Updated_at: 2024-08-24 12:18:04 UTC  
> Url: https://github.com/boostorg/wave/pull/214#discussion_r1729950815  

I see from later changes that it's the various lexers :)


---

## Comment 4

> Username: jefftrull  
> Created_at: 2024-08-24 12:22:47 UTC  
> Url: https://github.com/boostorg/wave/pull/214#issuecomment-2308376476  

This is great work and I appreciate the effort you've put into making this possible. Before I merge, though, I wanted to ask about the current CI failures - will they clear up soon? Some sort of gcc version issue with a Docker container?

---

## Comment 5

> Username: jefftrull  
> Created_at: 2024-08-26 05:16:05 UTC  
> Url: https://github.com/boostorg/wave/pull/214#issuecomment-2309335153  

AFAICT the libc version issues in CI are  unrelated and affecting other Boost libs. I'm going to track them down first so I have a green build to start from.

---
