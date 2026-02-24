# #531 Add -std=c++11 flag because sysinfo uses c++11 features [Merged]

> Username: vahtis  
> Created at: 2020-01-29 11:29:04 UTC  
> Updated at: 2021-10-02 21:13:32 UTC  
> Merged at: 2020-01-29 15:11:26 UTC  
> Closed at: 2020-01-29 15:11:26 UTC  
> Url: https://github.com/boostorg/build/pull/531  

Fixes #530   
  
src/engine/build.sh  
Added -std=c++11 flag to compile flags

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-01-29 12:51:55 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/531#pullrequestreview-350081658  

📁 src/engine/build.sh

```diff
 316 |         B2_CXX="${CXX}"
 317 |-         B2_CXXFLAGS_RELEASE="-xO4 -s"
 317 |+         B2_CXXFLAGS_RELEASE="-xO4 -s -std=c++11"
```

> Username: grafikrobot  
> Created_at: 2020-01-29 12:51:41 UTC  
> Updated_at: 2020-01-29 13:49:16 UTC  
> Url: https://github.com/boostorg/build/pull/531#discussion_r372363877  

The `B2_CXXFLAGS_RELEASE` is for additional optimization flags. You need to move the new option to the `B2_CXX` var.

> Username: vahtis  
> Created_at: 2020-01-29 13:50:25 UTC  
> Updated_at: 2020-01-29 13:50:26 UTC  
> Url: https://github.com/boostorg/build/pull/531#discussion_r372391999  

Done


---
