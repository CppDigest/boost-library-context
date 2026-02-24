# #94 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:32:06 UTC  
> Updated at: 2024-08-18 22:33:17 UTC  
> Merged at: 2024-08-18 22:33:17 UTC  
> Closed at: 2024-08-18 22:33:17 UTC  
> Url: https://github.com/boostorg/winapi/pull/94  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 22:50:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/winapi/pull/94#pullrequestreview-2190073466  

📁 test/Jamfile.v2

```diff
  79 |     local all_rules ;
  80 |     local file ;
  76 |-     local headers_path = [ path.make $(BOOST_ROOT)/libs/winapi/include/boost/winapi ] ;
```

> Username: Lastique  
> Created_at: 2024-07-20 22:50:38 UTC  
> Updated_at: 2024-07-20 22:50:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/94#discussion_r1685547301  

I don't think this is correct, `headers_path` is used below.

> Username: grafikrobot  
> Created_at: 2024-07-20 22:53:13 UTC  
> Url: https://github.com/boostorg/winapi/pull/94#discussion_r1685547471  

I'll investigate. Thanks for checking.


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 16:03:27 UTC  
> Url: https://github.com/boostorg/winapi/pull/94#issuecomment-2295311190  

Please review and merge this PR at your earliest convenience.

---
