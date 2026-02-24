# #268 Properly handle python include path [Closed]

> Username: yoonghm  
> Created at: 2019-03-29 01:28:36 UTC  
> Updated at: 2023-12-06 02:47:27 UTC  
> Closed at: 2023-12-06 02:47:27 UTC  
> Url: https://github.com/boostorg/boost/pull/268  

Python include path was not detected earlier. This solution should fix it.

---

## Comment 1

> Username: yoonghm  
> Created_at: 2019-04-14 02:29:09 UTC  
> Url: https://github.com/boostorg/boost/pull/268#issuecomment-482913331  

Travis could not verify the changes. Please review or do a manually build to verify. This pull request does handle python path properly.

---

## Review 2 [Changes requested]

> Username: tanzislam  
> Created_at: 2019-06-03 11:05:31 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boost/pull/268#pullrequestreview-244812470  

📁 bootstrap.sh

```diff
 366 | {
 363 |-     using python : $PYTHON_VERSION : $PYTHON_ROOT ;
 367 |+     using python : $PYTHON_VERSION : $PYTHON_ROOT : $PYTHON_INCLUDE ;
```

> Username: tanzislam  
> Created_at: 2019-06-03 11:04:34 UTC  
> Updated_at: 2019-06-03 11:05:31 UTC  
> Url: https://github.com/boostorg/boost/pull/268#discussion_r289792403  

Consider double-quoting `$PYTHON_INCLUDE` to allow it to work on Windows. (I'm also double-quoting `$PYTHON_ROOT` in #300.)


---
