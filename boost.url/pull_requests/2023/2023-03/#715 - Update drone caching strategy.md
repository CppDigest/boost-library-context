# #715 Update drone caching strategy [Merged]

> Username: sdarwin  
> Created at: 2023-03-16 16:43:18 UTC  
> Updated at: 2023-03-21 19:20:31 UTC  
> Merged at: 2023-03-21 19:20:31 UTC  
> Closed at: 2023-03-21 19:20:31 UTC  
> Url: https://github.com/boostorg/url/pull/715  

A proposed change to the caching methodology.  
  
The cache key has been the boostorg/boost commit SHA, which changes around every 4 hours and thus invalidates the cache most of the times you attempt to use it. If a few hours have passed and there is a pull request or a commit, those will be cache misses.  
  
The new strategy is to basically always use the cache.   
  
common_install.sh runs every time. "git pull" or "submodule update" only take a few seconds because they don't need to download a full git repository. b2 recompilation will be skipped.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-16 17:00:06 UTC  
> Updated_at: 2023-03-20 18:40:12 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472357100  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/715?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#715](https://codecov.io/gh/boostorg/url/pull/715?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b42ccac) into [develop](https://codecov.io/gh/boostorg/url/commit/565de153d809e4d4d56771fb69c7bc25ecced1fc?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (565de15) will **increase** coverage by `0.10%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/715/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/715?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #715      +/-   ##  
===========================================  
+ Coverage    97.11%   97.21%   +0.10%       
===========================================  
  Files          155      155                
  Lines         8461     8486      +25       
===========================================  
+ Hits          8217     8250      +33       
+ Misses         244      236       -8       
```  
  
  
[see 7 files with indirect coverage changes](https://codecov.io/gh/boostorg/url/pull/715/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/715?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/715?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [565de15...b42ccac](https://codecov.io/gh/boostorg/url/pull/715?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-03-16 17:05:21 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472367263  

GCOVR code coverage report [https://715.url.prtest.cppalliance.org/gcovr/index.html](https://715.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://715.url.prtest.cppalliance.org/genhtml/index.html](https://715.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2023-03-16 17:18:09 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472388027  

Running "git pull" or "submodule update" every time is a good idea. Then we can recache the newer versions.

---

## Review 4 [Commented]

> Username: alandefreitas  
> Created_at: 2023-03-16 17:20:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/715#pullrequestreview-1344364551  

📁 .drone.star

```diff
 412 |             environment['drone_cache_mount'] = cache_dir
 413 |-             if image in images_used:
 413 |+             if image in images_used or buildtype != "boost":
```

> Username: alandefreitas  
> Created_at: 2023-03-16 17:20:23 UTC  
> Updated_at: 2023-03-16 17:20:24 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139097601  

Who's expected to build the original cache on which we "git pull" or "submodule update" for the first time?  
  
Don't we still need `drone_cache_rebuild` in one job to upload the new libs with "git pull" or "submodule update" work?  
  
We "git pull" or "submodule update" do not do anything, can we set `drone_cache_rebuild` from `true` to `false` to skip the upload step?

> Username: sdarwin  
> Created_at: 2023-03-16 17:59:32 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139165387  

@alandefreitas  
notice in this modification,   
  
> if image in images_used or buildtype != "boost":  
  
"!=" instead of a "=". The effect is excluding "docs" from being the rebuilder instance. "boost" will rebuild.  
  
> Who's expected to build the original cache on which we "git pull" or "submodule update" for the first time?  
  
common_install.sh runs every time, regardless, and creates the original boost-root folder.  
  
> Don't we still need drone_cache_rebuild   
  
drone_cache_rebuild is still present. Hasn't been removed from the procedure.  
  
> When "git pull" or "submodule update" do not do anything, can we set drone_cache_rebuild from true to false to skip the upload step?  
  
this is the same issue that has been present for a while. It only affects the rebuilder instances, and only takes 5 seconds. Not so bad. I will open an issue with meltwater but probably we can live with that delay.

> Username: alandefreitas  
> Created_at: 2023-03-16 18:21:01 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139196836  

> "!=" instead of a "=". The effect is excluding "docs" from being the rebuilder instance. "boost" will rebuild.  
  
Oh... sorry. So this is just fixing something else in passing. Great.  
  
> only takes 5 seconds  
  
:+1:

> Username: sdarwin  
> Created_at: 2023-03-16 18:41:12 UTC  
> Updated_at: 2023-03-16 18:41:13 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139220679  

>  issue with meltwater  
  
actually, I will delay asking them, because it's likely beyond their ability to fix right now, I have already opened an issue to be able to cache absolute paths instead of relative paths which would allow boost-root to be immediately cached instead of copying it back and forth.

> Username: alandefreitas  
> Created_at: 2023-03-16 19:36:29 UTC  
> Updated_at: 2023-03-16 19:36:30 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139274118  

Sounds good


---

## Review 5 [Commented]

> Username: alandefreitas  
> Created_at: 2023-03-16 20:51:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/715#pullrequestreview-1344694844  

📁 .drone/drone.sh

```diff
 135 |-     export BOOST_ROOT
 136 |-     ls "$cache_dir/boost"
 130 |+     export BOOST_ROOT="$(pwd)"
```

> Username: alandefreitas  
> Created_at: 2023-03-16 20:51:46 UTC  
> Updated_at: 2023-03-16 20:51:47 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139346618  

This is effectively just removing the `ls "$cache_dir/boost"` line above.  
  
I've been splitting commands such as `export BOOST_ROOT="$(pwd)"`  into   
  
```  
BOOST_ROOT="$(pwd)"  
    export BOOST_ROOT  
```  
  
because shellcheck gives me the error [SC2155](https://www.shellcheck.net/wiki/SC2155). Not a huge problem but still annoying because it hides more serious problems.

> Username: sdarwin  
> Created_at: 2023-03-16 21:16:37 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139367066  

shellcheck. Ok switched it back. The commits should be squashed.


---

## Review 6 [Commented]

> Username: alandefreitas  
> Created_at: 2023-03-16 20:52:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/715#pullrequestreview-1344696316  

📁 .drone/drone.sh

```diff
 139 |+   if [ "$drone_cache_rebuild" == true ]; then
 140 |+     if command -v apt-get &>/dev/null; then
 141 |+       apt-get install -y rsync
```

> Username: alandefreitas  
> Created_at: 2023-03-16 20:52:57 UTC  
> Updated_at: 2023-03-16 20:52:58 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139347597  

Should the images include `rsync`? I couldn't replicate the command with `cp` but this conditional looks like a workaround.

> Username: sdarwin  
> Created_at: 2023-03-16 21:18:58 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139368848  

The images should include `rsync` but they don't at the moment. This workaround isn't too bad.


---

## Review 7 [Commented]

> Username: alandefreitas  
> Created_at: 2023-03-16 20:53:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/715#pullrequestreview-1344697457  

📁 .drone/drone.sh

```diff
 135 |+     cd $DRONE_WORKSPACE
 136 |+   fi
 137 |+   . ./ci/common_install.sh
```

> Username: alandefreitas  
> Created_at: 2023-03-16 20:53:51 UTC  
> Url: https://github.com/boostorg/url/pull/715#discussion_r1139348351  

That's quite nice. It does the whole thing properly in both paths.


---

## Comment 8

> Username: alandefreitas  
> Created_at: 2023-03-16 20:56:50 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472727964  

This strategy is quite smart :)  
  
The cache baseline only needs to change when there's a new tag, depinst.py ensures everything is up to date, and anything updated is reached. Perfect.

---

## Comment 9

> Username: sdarwin  
> Created_at: 2023-03-16 21:25:32 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472765092  

> only needs to change when there's a new tag  
  
By the way, that's not strictly required. It seems to be a nice idea to refresh on a git tag, every three or four months. But conceivably you could remove the check and _never_ recreate the cache so it would be a perpetual eternal cache because "git pull" is being run every time.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-03-16 21:29:14 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472769535  

GCOVR code coverage report [https://715.url.prtest.cppalliance.org/gcovr/index.html](https://715.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://715.url.prtest.cppalliance.org/genhtml/index.html](https://715.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: alandefreitas  
> Created_at: 2023-03-16 23:04:26 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472875550  

https://drone.cpp.al/boostorg/url/2166

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-03-17 00:44:30 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1472946603  

GCOVR code coverage report [https://715.url.prtest.cppalliance.org/gcovr/index.html](https://715.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://715.url.prtest.cppalliance.org/genhtml/index.html](https://715.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: sdarwin  
> Created_at: 2023-03-20 18:22:24 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1476728942  

caching is currently implemented as a container 'meltwater/drone-cache' which means the function `image_supports_caching` should check `image_str != None`

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-03-20 18:34:42 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1476743986  

GCOVR code coverage report [https://715.url.prtest.cppalliance.org/gcovr/index.html](https://715.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://715.url.prtest.cppalliance.org/genhtml/index.html](https://715.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://715.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: alandefreitas  
> Created_at: 2023-03-20 19:23:22 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1476811532  

> image_supports_caching should check image_str != None  
  
Definitely. This is a plain error.  
  
Is this PR good to go in your opinion?

---

## Comment 16

> Username: sdarwin  
> Created_at: 2023-03-20 19:27:01 UTC  
> Url: https://github.com/boostorg/url/pull/715#issuecomment-1476815935  

Each time we find something new, but yes it should be good-to-go.

---
