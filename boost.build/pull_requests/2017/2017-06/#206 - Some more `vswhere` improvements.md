# #206 Some more `vswhere` improvements [Merged]

> Username: refack  
> Created at: 2017-06-27 15:23:09 UTC  
> Updated at: 2021-10-02 22:08:41 UTC  
> Merged at: 2017-08-09 14:03:06 UTC  
> Closed at: 2017-08-09 14:03:06 UTC  
> Url: https://github.com/boostorg/build/pull/206  

* First commit is just a precaution  
* Second commit handles a new feature of `vswhere` https://github.com/Microsoft/vswhere/issues/79 if you want to detect prerelease versions of VS2017 you'll need to call `vswhere_usability_wrapper.cmd prerelease`

---

## Comment 1

> Username: refack  
> Created_at: 2017-06-27 15:26:33 UTC  
> Url: https://github.com/boostorg/build/pull/206#issuecomment-311394168  

@grafikrobot if you want to "hard" ping me I'm available at the email listed in https://github.com/nodejs/node#collaborators (gmail @ hangouts)

---

## Comment 2

> Username: refack  
> Created_at: 2017-06-27 15:35:25 UTC  
> Url: https://github.com/boostorg/build/pull/206#issuecomment-311396822  

Another tiny tweak, the user can `set VSWHERE_WITH_PRERELASE=1` to trigger prerelease detection with out manipulating the batch files

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2017-07-18 00:36:52 UTC  
> Url: https://github.com/boostorg/build/pull/206#issuecomment-315924252  

@refack This still fails for non-2017 VS.

---

## Comment 4

> Username: refack  
> Created_at: 2017-07-18 01:50:36 UTC  
> Url: https://github.com/boostorg/build/pull/206#issuecomment-315934742  

Looking into this

---

## Comment 5

> Username: refack  
> Created_at: 2017-07-18 10:28:33 UTC  
> Url: https://github.com/boostorg/build/pull/206#issuecomment-316023389  

Rebased, and bumped `vswhere_usability_wrapper.cmd` to latest version (2 bug fixes since the version that was previously in this PR)

---

## Comment 6

> Username: refack  
> Created_at: 2017-07-18 11:05:31 UTC  
> Url: https://github.com/boostorg/build/pull/206#issuecomment-316031108  

Offf, still broken... Continuing work...

---

## Comment 7

> Username: refack  
> Created_at: 2017-07-31 20:04:44 UTC  
> Url: https://github.com/boostorg/build/pull/206#issuecomment-319180523  

@grafikrobot found the bug.

---

## Review 8 [Commented]

> Username: grafikrobot  
> Created_at: 2017-08-01 16:19:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/206#pullrequestreview-53555008  

📁 src/engine/config_toolset.bat

```diff
 163 | if NOT "_%BOOST_JAM_TOOLSET%_" == "_vc141_" goto Skip_VC141
 164 | call vswhere_usability_wrapper.cmd
 165 |+ :: Reset ERRORLEVEL since from now on it's all based on ENV vars
```

> Username: grafikrobot  
> Created_at: 2017-08-01 16:18:54 UTC  
> Updated_at: 2017-08-01 19:28:43 UTC  
> Url: https://github.com/boostorg/build/pull/206#discussion_r130654599  

Is "::" a valid replacement for "REM"? Or is that just a CMD'ism?

> Username: refack  
> Created_at: 2017-08-01 19:24:12 UTC  
> Updated_at: 2017-08-01 19:28:43 UTC  
> Url: https://github.com/boostorg/build/pull/206#discussion_r130703621  

Valid replacement - https://ss64.com/nt/rem.html.  
Used by M$ - https://github.com/dotnet/corefx/blob/master/run.cmd

> Username: refack  
> Created_at: 2017-08-01 19:27:00 UTC  
> Updated_at: 2017-08-01 19:28:43 UTC  
> Url: https://github.com/boostorg/build/pull/206#discussion_r130704256  

I'll replace to be consistent.


---
