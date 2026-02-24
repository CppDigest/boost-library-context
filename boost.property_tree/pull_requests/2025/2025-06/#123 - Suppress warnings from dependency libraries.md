# #123 Suppress warnings from dependency libraries [Merged]

> Username: ashtum  
> Created at: 2025-06-09 11:30:15 UTC  
> Updated at: 2025-06-30 11:53:18 UTC  
> Merged at: 2025-06-09 16:11:22 UTC  
> Closed at: 2025-06-09 16:11:22 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-06-09 14:03:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/property_tree/pull/123#pullrequestreview-2910178222  

📁 build.jam

```diff
  23 | 
  24 | project /boost/property_tree
  25 |+     : common-requirements
```

> Username: pdimov  
> Created_at: 2025-06-09 14:03:38 UTC  
> Updated_at: 2025-06-09 14:03:39 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123#discussion_r2135787285  

You shouldn't do this; the other way is the correct one, which is why we switched to it.


---

## Comment 2

> Username: ashtum  
> Created_at: 2025-06-09 16:04:00 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123#issuecomment-2956232294  

@pdimov, I don't have merge access. please go ahead and merge this if it looks good to you.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-06-30 10:53:42 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123#issuecomment-3018696532  

Why did you remove `-j1` from the b2 invocations in this PR? This leads to sporadic failures in test_xml_parser_rapidxml.

---

## Comment 4

> Username: ashtum  
> Created_at: 2025-06-30 11:10:57 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123#issuecomment-3018746292  

> Why did you remove `-j1` from the b2 invocations in this PR? This leads to sporadic failures in test_xml_parser_rapidxml.  
  
Ah, sorry. I couldn’t find anything in the commit message and guessed it might have been related to old bugs that should have been fixed by now.

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-06-30 11:16:03 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123#issuecomment-3018761187  

The problem is that test_xml_parser_rapidxml.cpp writes out xml files with fixed names, so when two instances of this test are run in parallel, they step on each other's toes. To remove -j1 the test needs to be fixed to not do that, e.g. by adding a random prefix to the file names, or by using a temporary directory with a random name.

---

## Comment 6

> Username: ashtum  
> Created_at: 2025-06-30 11:39:42 UTC  
> Updated_at: 2025-06-30 11:39:56 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123#issuecomment-3018826378  

> The problem is that test_xml_parser_rapidxml.cpp writes out xml files with fixed names, so when two instances of this test are run in parallel, they step on each other's toes. To remove -j1 the test needs to be fixed to not do that, e.g. by adding a random prefix to the file names, or by using a temporary directory with a random name.  
  
It might be as simple as adding random prefixes to the file names passed to this function:  
https://github.com/boostorg/property_tree/blob/5c5e2c9953315dd39b13d1d263bf6f00a4752a7a/test/test_utils.hpp#L108-L116  
  
I can test it, and if it doesn't work, we can settle for adding `-j1` because I doubt it's worth putting in more time, as we don't frequently make changes to this repository.

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-06-30 11:53:17 UTC  
> Url: https://github.com/boostorg/property_tree/pull/123#issuecomment-3018863687  

I already put back the -j1 to fix the CI failures, but we can in principle apply this fix, if it works.

---
