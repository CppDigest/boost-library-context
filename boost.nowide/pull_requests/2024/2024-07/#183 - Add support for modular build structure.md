# #183 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:34:23 UTC  
> Updated at: 2025-04-05 15:54:42 UTC  
> Merged at: 2025-04-05 15:54:42 UTC  
> Closed at: 2025-04-05 15:54:42 UTC  
> Url: https://github.com/boostorg/nowide/pull/183  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:06:22 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#issuecomment-2295312074  

Please review and merge this PR at your earliest convenience.

---

## Review 2 [Changes requested]

> Username: Flamefire  
> Created_at: 2024-08-25 12:41:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/nowide/pull/183#pullrequestreview-2259238382  

It seems that now the warning settings get propagated to dependencies which cause failures when build libraries that have a looser warning setting than this one.  
  
I.e. I want this library to emit no warnings testing it via `-Werror` but ignore any warnings from dependencies, especially those in the sources of the dependencies, e.g. now in `libs\filesystem\src\operations.cpp`, see https://github.com/boostorg/filesystem/issues/321  
  
Can this be done?  
  
The GHA failures should be fixed in develop, no need to worry about those.

📁 build.jam

```diff
  10 |+ feature.feature boost.nowide.lfs : auto no : optional propagated ;
  11 |+ 
  12 |+ constant boost_dependencies :
```

> Username: Flamefire  
> Created_at: 2024-08-25 12:37:01 UTC  
> Updated_at: 2024-08-25 12:41:48 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#discussion_r1730327788  

Any reason for the variable instead of inlining in build/Jamfile?

> Username: grafikrobot  
> Created_at: 2024-10-12 16:15:33 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#discussion_r1797726247  

The only reason is to advertise the dependencies to others consistently for both buildable and header only libs.

> Username: Flamefire  
> Created_at: 2024-10-13 09:28:12 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#discussion_r1798196864  

So that's intended for ("others"=)humans, isn't it? Because otherwise I don't see how [this](https://github.com/boostorg/nowide/pull/183/files#diff-322788b77c72dedcc9b4a12bf0233ceb8451b4a6baa6d851173ecd5320ac14aeR21) is consistent with e.g. https://github.com/boostorg/variant2/blob/a4b167b7238781d75df5d6684c9e9d40b26456dc/build.jam#L17 given that are different files  
  
My hesitation is because now when reading/editing the build/Jamfile I need to switch to this to see the definition of a variable used there. For head-only libs it is defined and used in the same file (as the build-Jamfile doesn't exist)  
  
To be clear: I'm not saying this is bad, I just want to understand the motivation why.

> Username: grafikrobot  
> Created_at: 2025-04-05 12:22:01 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#discussion_r2029855996  

Yes, it's mainly for people reading the file. I understand the concern though. The attempt is to make this uniform for all libraries (it was easier to generate all of this info this way also).


---

## Comment 3

> Username: Flamefire  
> Created_at: 2024-08-27 14:33:46 UTC  
> Updated_at: 2024-08-27 14:34:36 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#issuecomment-2312744037  

@grafikrobot Anything new about not "up-propagating" warning settings? See https://github.com/boostorg/nowide/actions/runs/10547775762/job/29220866970?pr=183

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-08-27 16:53:24 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#issuecomment-2313071144  

## [Codecov](https://app.codecov.io/gh/boostorg/nowide/pull/183?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.45%. Comparing base [(`43f53ec`)](https://app.codecov.io/gh/boostorg/nowide/commit/43f53ecc30e37aa8df3bdeb68e1d00c7ef2f5d1e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`756e3dc`)](https://app.codecov.io/gh/boostorg/nowide/commit/756e3dc5ad91f5662a08a3274d857e2034b58632?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #183   +/-   ##  
========================================  
  Coverage    99.45%   99.45%             
========================================  
  Files           34       34             
  Lines         3319     3319             
========================================  
  Hits          3301     3301             
  Misses          18       18             
```  
  
</details>

---

## Comment 5

> Username: Flamefire  
> Created_at: 2024-10-11 11:44:24 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#issuecomment-2407239122  

@grafikrobot See https://github.com/boostorg/nowide/pull/183/files#r1730327788  
  
Why is `boost_dependencies` a constant and not just inlined in the single place it is used?

---

## Review 6 [Changes requested]

> Username: Flamefire  
> Created_at: 2025-04-05 08:55:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/nowide/pull/183#pullrequestreview-2744830283  

📁 build.jam

```diff
  12 |+ constant boost_dependencies :
  13 |+     /boost/config//boost_config
  14 |+     /boost/filesystem//boost_filesystem ;
```

> Username: Flamefire  
> Created_at: 2025-04-05 08:55:42 UTC  
> Updated_at: 2025-04-05 08:55:54 UTC  
> Url: https://github.com/boostorg/nowide/pull/183#discussion_r2029816463  

This requirement isn't really correct and is causing the failure on Appveyor.  
This is only referenced in an optional header provided for convenience and hence not a hard requirement and used only [in a test](https://github.com/boostorg/nowide/pull/183/files#diff-1f3dccb3928ad7b52e4c986270cb619b663197283b764c2fb68256d285f21a3fR37) where it is included with werror disable as Boost.FileSystem isn't warning free.  
  
Can this be removed? If so I'd leave the semicolon on a separate line for smaller future diffs as in other instances  
```diff  
-    /boost/filesystem//boost_filesystem ;  
+    ;  
```


---
