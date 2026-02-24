# #218 update CMake test subdirs [Merged]

> Username: alandefreitas  
> Created at: 2022-07-01 07:10:26 UTC  
> Updated at: 2022-07-01 17:09:20 UTC  
> Merged at: 2022-07-01 07:37:28 UTC  
> Closed at: 2022-07-01 07:37:29 UTC  
> Url: https://github.com/boostorg/url/pull/218  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-01 11:55:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/218#pullrequestreview-1026119940  

📁 CMakeLists.txt

```diff
 123 |     )
 124 |+ 
 125 |+     install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/boost
```

> Username: vinniefalco  
> Created_at: 2022-07-01 11:55:31 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r911895641  

@pdimov is this ok?

> Username: pdimov  
> Created_at: 2022-07-01 14:47:49 UTC  
> Updated_at: 2022-07-01 14:47:50 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r912029218  

Libraries shouldn't be installing anything when not root project.

> Username: vinniefalco  
> Created_at: 2022-07-01 15:02:35 UTC  
> Updated_at: 2022-07-01 15:02:36 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r912041052  

Seems like the answer is "no", because the `install` line happens when `NOT BOOST_SUPERPROJECT_VERSION` which means not root.

> Username: alandefreitas  
> Created_at: 2022-07-01 16:57:14 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r912118395  

You guys are probably (i) talking about different roots or (ii) the third comment misrepresents the second.  
  
@pdimov, "Libraries shouldn't be installing anything when not root project." means "Boost.URL shouldn't be installing anything when (i) **`boost/`** is root and (ii) **`boost/libs/url`** is not root.", right? @vinniefalco, `NOT BOOST_SUPERPROJECT_VERSION` means (i) **`boost/`** is not root and (ii) **`boost/libs/url`** is root, which is the opposite.   
  
(Even though an orthogonal issue is that we should start identifying whether the project is root with `BOOST_URL_IS_ROOT` instead of `NOT BOOST_SUPERPROJECT_VERSION`, which the scripts do everywhere. (e.g. [line 30](https://github.com/CPPAlliance/url/blob/ef2db3c1e52474b881812da92ae495cd936ed150/CMakeLists.txt#L30)).)  
  
In any case, whether the library *should* be installing anything and under what conditions is not the most relevant issue here. This is a tangent because:  
  
- This change fixes an installation script that *was already there* even though it was wrong. And this target was causing a problem elsewhere, which is what the PR is about. So the PR is agnostic about the merits of an `install` target. All the PR is assuming is that **if** the library installs something (when in **`boost/libs/url`**root and not in **`boost/`** root), then such installation should be working. It was not.   
- Even if the PR was not agnostic about that, and if libraries **MUST NOT** be installing **anything**, then we would have to remove the whole thing, and not only revert this commit and leave behind an `install` target that doesn't work.   
- And even if libraries MUST NOT be installing anything _only when_ the root is `boost/`, then we have to know if libraries **SHOULD** be installing anything when the root is `boost/libs/<library>`. Many boost modules include install targets (when the root is not `boost/`), and the best we can do here is to check if this is "officially" desirable.

> Username: vinniefalco  
> Created_at: 2022-07-01 16:58:28 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r912119025  

tl;dr

> Username: alandefreitas  
> Created_at: 2022-07-01 17:01:37 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r912120841  

> tl;dr  
  
This is not a bad commit at all. You probably misunderstood what Peter said and definitely misunderstood what the PR is about.

> Username: pdimov  
> Created_at: 2022-07-01 17:05:15 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r912122876  

My recommendation remains that libraries should not be installing anything when not root project. This is not limited to the case where they reside under the Boost superproject. It also applies to them being added as a subproject with `add_subdirectory` or `FetchContent`, without the Boost superproject.

> Username: alandefreitas  
> Created_at: 2022-07-01 17:09:20 UTC  
> Url: https://github.com/boostorg/url/pull/218#discussion_r912125152  

Yes. The library uses `NOT BOOST_SUPERPROJECT_VERSION` to identify when root project. We need to change that to `BOOST_URL_IS_ROOT`.  
  
It's not what this PR is about anyway. This PR just fixes the install target that already exists because it was causing a problem elsewhere.


---
