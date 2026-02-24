# #108 GitHub Actions config [Closed]

> Username: sdarwin  
> Created at: 2021-03-03 14:56:55 UTC  
> Updated at: 2021-12-08 15:41:35 UTC  
> Closed at: 2021-12-08 15:41:35 UTC  
> Url: https://github.com/boostorg/parameter/pull/108  

GitHub Actions CI script, generated from the .travis.yml file. Please refer to https://github.com/CPPAlliance/githubactions for more information and instructions.

---

## Review 1 [Commented]

> Username: urandon  
> Created_at: 2021-06-09 21:41:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/parameter/pull/108#pullrequestreview-680172018  

📁 .github/workflows/ci.yml

```diff
  20 |+           - name: "TOOLSET=gcc COMPILER=g++-4.4 CXXSTD=98,0x Job 0"
  21 |+             buildtype: "boost"
  22 |+             packages: "g++-4.4"
```

> Username: urandon  
> Created_at: 2021-06-09 21:41:42 UTC  
> Url: https://github.com/boostorg/parameter/pull/108#discussion_r648704592  

Is there any real reason to spent public CI resources on outdated g++4.4?  
The same is related to other outdated and unsupported compilers

> Username: sdarwin  
> Created_at: 2021-06-09 21:49:37 UTC  
> Url: https://github.com/boostorg/parameter/pull/108#discussion_r648708607  

The file was automatically generated based on the existing .travis.yml which seems to still have g++4.4.    You are welcome to delete any outdated compilers.

> Username: Lastique  
> Created_at: 2021-06-09 23:00:54 UTC  
> Url: https://github.com/boostorg/parameter/pull/108#discussion_r648738566  

I don't think removing support for any compilers in Boost.Parameter was discussed or announced anywhere. I'd prefer as many compilers tested as possible.


---

## Comment 2

> Username: Lastique  
> Created_at: 2021-12-08 15:41:35 UTC  
> Url: https://github.com/boostorg/parameter/pull/108#issuecomment-988926773  

The support for GHA has already been added. Thanks.

---
