# #21 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:35:40 UTC  
> Updated at: 2024-08-18 23:10:27 UTC  
> Merged at: 2024-08-18 23:10:27 UTC  
> Closed at: 2024-08-18 23:10:27 UTC  
> Url: https://github.com/boostorg/scope/pull/21  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-07-20 22:45:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/scope/pull/21#pullrequestreview-2190072407  

📁 test/Jamfile

```diff
  13 | import os ;
  11 |- import ../../config/checks/config : requires ;
  14 |+ import-search /boost/config/checks ;
```

> Username: Lastique  
> Created_at: 2024-07-20 22:45:17 UTC  
> Url: https://github.com/boostorg/scope/pull/21#discussion_r1685546602  

Is this duplicate line intentional?

> Username: grafikrobot  
> Created_at: 2024-07-20 22:51:54 UTC  
> Url: https://github.com/boostorg/scope/pull/21#discussion_r1685547371  

No, it's not intentional. Will fix!


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-18 16:07:56 UTC  
> Url: https://github.com/boostorg/scope/pull/21#issuecomment-2295312598  

Please review and merge this PR at your earliest convenience.

---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2024-08-18 22:38:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/scope/pull/21#pullrequestreview-2244424735  

📁 test/Jamfile

```diff
  61 |     {
  56 |-         local headers_path = [ path.make $(BOOST_ROOT)/libs/scope/include/boost/scope ] ;
  62 |+         local headers_path = [ path.make $(SCOPE_INCLUDE_DIR)/boost/scope ] ;
```

> Username: Lastique  
> Created_at: 2024-08-18 22:38:53 UTC  
> Url: https://github.com/boostorg/scope/pull/21#discussion_r1721078080  

This could be changed just to "../boost/scope", right? `SCOPE_INCLUDE_DIR` seems to be unused anywhere else.

> Username: grafikrobot  
> Created_at: 2024-08-18 22:54:32 UTC  
> Updated_at: 2024-08-18 22:54:33 UTC  
> Url: https://github.com/boostorg/scope/pull/21#discussion_r1721080453  

Not in this case. As the `path.glob-tree` rule works with absolute paths. And an effect of the `path-constant` call is to get you the absolute path to base from.


---
