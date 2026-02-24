# #517 Tentatively fix Circle-ci build. [Merged]

> Username: jzmaddock  
> Created at: 2025-05-06 08:45:54 UTC  
> Updated at: 2025-05-06 14:55:54 UTC  
> Merged at: 2025-05-06 10:06:26 UTC  
> Closed at: 2025-05-06 10:06:26 UTC  
> Url: https://github.com/boostorg/config/pull/517  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2025-05-06 10:04:24 UTC  
> Url: https://github.com/boostorg/config/pull/517#issuecomment-2853982459  

Remaining failure is a network issue on drone, merging.

---

## Review 2 [Commented]

> Username: grafikrobot  
> Created_at: 2025-05-06 13:44:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/517#pullrequestreview-2818349069  

📁 .circleci/config.yml

```diff
   8 |     docker:
   9 |-       - image: gcc:7
   9 |+       - image: gcc:13
```

> Username: grafikrobot  
> Created_at: 2025-05-06 13:44:55 UTC  
> Url: https://github.com/boostorg/config/pull/517#discussion_r2075516258  

Where does one find what images are available? I spent many minutes reading CircleCI docs and I didn't find gcc or clang specific images.

> Username: jzmaddock  
> Created_at: 2025-05-06 14:55:19 UTC  
> Updated_at: 2025-05-06 14:55:20 UTC  
> Url: https://github.com/boostorg/config/pull/517#discussion_r2075662341  

I believe they are the official docker images rather than circleci ones.  But I genuinely have no idea.


---

## Review 3 [Commented]

> Username: grafikrobot  
> Created_at: 2025-05-06 13:47:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/517#pullrequestreview-2818359209  

📁 .circleci/config.yml

```diff
  48 |           command: |
  49 |-             cd $BOOST/boost/tools/inspect/build && ../../../b2 -j2 address-model=64 architecture=x86 toolset=gcc cxxflags="-std=gnu++14" release dist-bin
  49 |+             cd $BOOST/boost/tools/inspect && ../../b2 -j2 address-model=64 architecture=x86 toolset=gcc cxxflags="-std=gnu++14" release dist-bin
```

> Username: grafikrobot  
> Created_at: 2025-05-06 13:47:13 UTC  
> Url: https://github.com/boostorg/config/pull/517#discussion_r2075520934  

It would be better to use the `cxxstd=14` feature instead of the flag directly.

> Username: jzmaddock  
> Created_at: 2025-05-06 14:55:54 UTC  
> Url: https://github.com/boostorg/config/pull/517#discussion_r2075663519  

Good point, fossilized old code lurking, I'll update next time I'm in there.


---
