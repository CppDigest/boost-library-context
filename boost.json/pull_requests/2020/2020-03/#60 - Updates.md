# #60 Updates [Merged]

> Username: vinniefalco  
> Created at: 2020-03-17 21:04:08 UTC  
> Updated at: 2020-03-18 09:46:00 UTC  
> Merged at: 2020-03-17 21:54:38 UTC  
> Closed at: 2020-03-17 21:54:38 UTC  
> Url: https://github.com/boostorg/json/pull/60  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-17 21:50:19 UTC  
> Updated_at: 2020-03-17 22:57:04 UTC  
> Url: https://github.com/boostorg/json/pull/60#issuecomment-600318115  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/60?src=pr&el=h1) Report  
> Merging [#60](https://codecov.io/gh/CPPAlliance/json/pull/60?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/47bbedcd1cfcdd74d446f35d09b391dc7cc9a6ca?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/60/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/CPPAlliance/json/pull/60?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #60   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4861     4861             
========================================  
  Hits          4817     4817             
  Misses          44       44  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/60?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/60?src=pr&el=footer). Last update [47bbedc...831cab9](https://codecov.io/gh/CPPAlliance/json/pull/60?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: Flamefire  
> Created_at: 2020-03-18 07:16:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/60#pullrequestreview-376610776  

📁 .github/workflows/cmake-find-boost-install.yml

```diff
  55 |+ 
  56 |+       - name: Test
  57 |+         run: |
```

> Username: Flamefire  
> Created_at: 2020-03-18 07:16:42 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394143790  

just because I love saving horizontal space: You can use `working-directory:` instead of the `cd` and make all `run:` simple one-liners (without the pipe)

> Username: mloskot  
> Created_at: 2020-03-18 09:03:45 UTC  
> Updated_at: 2020-03-18 09:03:46 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394193956  

Good tip, thanks.

> Username: mloskot  
> Created_at: 2020-03-18 09:42:20 UTC  
> Updated_at: 2020-03-18 09:42:21 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394216741  

Done #62


---

## Review 3 [Commented]

> Username: Flamefire  
> Created_at: 2020-03-18 07:18:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/60#pullrequestreview-376611677  

📁 .github/workflows/cmake-with-boost-stage.yml

```diff
  46 |+       - uses: actions/checkout@v2
  47 |+         with:
  48 |+           path: ${{ steps.setenv.outputs.boost_root }}/libs/${{ steps.setenv.outputs.boost_self }}
```

> Username: Flamefire  
> Created_at: 2020-03-18 07:18:32 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394144512  

You don't need outputs AND env: `${{env.BOOST_ROOT}}` works

> Username: mloskot  
> Created_at: 2020-03-18 08:54:06 UTC  
> Updated_at: 2020-03-18 08:54:07 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394188544  

Good tip, thanks

> Username: mloskot  
> Created_at: 2020-03-18 09:42:13 UTC  
> Updated_at: 2020-03-18 09:42:14 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394216668  

Done #62


---

## Review 4 [Commented]

> Username: Flamefire  
> Created_at: 2020-03-18 07:19:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/60#pullrequestreview-376612255  

📁 CMakeLists.txt

```diff
  16 | 
  17 |- project(boost_json VERSION ${BOOST_JSON_VERSION} LANGUAGES CXX)
  17 |+ if (BOOST_SUPERPROJECT_VERSION)
```

> Username: Flamefire  
> Created_at: 2020-03-18 07:19:45 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394144978  

Isn't this pointless given https://github.com/CPPAlliance/json/pull/60/files#diff-af3b638bc2a3e6c650974192a53c7291R13-R15

> Username: mloskot  
> Created_at: 2020-03-18 09:03:07 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394193605  

Sorry, no idea what this is referring to, lines 13-15 are  
  
```  
## Introduction  
  
This library provides containers and algorithms which implement JSON  
```

> Username: Flamefire  
> Created_at: 2020-03-18 09:06:20 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394195363  

Those lines are in the CML, Github doesn't show it by default as it is not part of the changeset AND jumps to the wrong file -.-:  
  
```  
if(BOOST_SUPERPROJECT_VERSION)  
    set(BOOST_JSON_VERSION ${BOOST_SUPERPROJECT_VERSION})  
endif()  
```

> Username: mloskot  
> Created_at: 2020-03-18 09:28:44 UTC  
> Updated_at: 2020-03-18 09:28:45 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394208553  

> Isn't this pointless given  
  
🤷‍♂ ask @vinniefalco   
  
The `BOOST_SUPERPROJECT_VERSION` handling was there before and I did not aim to remove it. I only patched it a bit.

> Username: Flamefire  
> Created_at: 2020-03-18 09:33:35 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394211642  

Yeah but the original version already took care of BOOST_SUPERPROJECT_VERSION and https://github.com/CPPAlliance/json/pull/60/commits/bbfb09b26c8acb105c02328a9222af2af384d94f added superflous BOOST_SUPERPROJECT_VERSION handling. So I'd just revert that commit. The original is more DRY

> Username: mloskot  
> Created_at: 2020-03-18 09:46:00 UTC  
> Url: https://github.com/boostorg/json/pull/60#discussion_r394218952  

Oh, good catch!  
  
Not sure what I was thinking about or it was left after some numerous experiments and CMakeLists.txt edits. Done in #62


---
