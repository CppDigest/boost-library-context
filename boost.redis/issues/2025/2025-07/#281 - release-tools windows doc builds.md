# #281 - release-tools windows doc builds [Open]

> Username: sdarwin  
> Created at: 2025-07-03 01:31:46 UTC  
> Updated at: 2025-10-22 17:46:24 UTC  
> Url: https://github.com/boostorg/redis/issues/281  

New redis docs were "Adapted from Boost.Unordered".  
  
How was Boost.Unordered able to build docs successfully with both Linux line-endings and Windows line-endings? That is a real question.   Probably the answer is this file:  
  
https://github.com/boostorg/unordered/blob/develop/.gitattributes  
  
Notice how it sets the line endings to "LF" for shell scripts `.sh`.  
  
Without this adjustment, redis breaks the superproject build on CRLF line-endings. windows builds.  
  
I'm running a `dos2unix` command on the files now, to see if that fixes the releases.   Try adding a CI job for docs on windows.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-07-03 09:10:50 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3031500625  

Sorry for the breakage. I will add the pipeline shortly. Please let me know whether `dos2unix` was enough or there is anything else to be fixed on our side.

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-07-03 10:13:42 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3031700986  

It looks like `dos2unix` works.   Builds succeeded.  https://app.circleci.com/pipelines/github/boostorg/boost  
  
As far as .gitattributes I wonder if just the lines that specify `.sh` would be enough?  A small file that only adjusts the shell scripts.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-07-03 10:42:41 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3031783886  

Great. Should we still add the .gitattributes file on our side?

---

## Comment 4

> Username: sdarwin  
> Created at: 2025-07-03 11:00:49 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3031845814  

> Should we still add the .gitattributes file on our side?  
  
Yes, since that would fix Windows builds using CRLF in all cases, even when dos2unix doesn't run, it's more robust.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-10-22 15:35:50 UTC  
> Updated at: 2025-10-22 15:36:18 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3433046272  

@sdarwin I'm trying to understand why this was a problem. I'm not getting what `release-tools` is really doing here. From [this line](https://github.com/boostorg/release-tools/blob/d27012edbe2516ce8c38798244a153a216ccba95/build_docs/windowsdocs.ps1#L795C13-L795C25) it seems to be invoking `build_antora.sh` by hand - why? The docs build in Boost.Redis, even if internally using Antora, should be invoked using `./b2 doc/libs/redis`. And AFAIK this applies to Unordered, too.

---

## Comment 6

> Username: sdarwin  
> Created at: 2025-10-22 15:44:29 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3433082451  

That line is not invoking build_antora.sh.   It's converting it to UNIX format.  `dos2unix .\build_antora.sh`  
  
"should be invoked using b2" : b2 calls doc/Jamfile, which in turn will run build_antora.sh.

---

## Comment 7

> Username: anarthal  
> Created at: 2025-10-22 16:44:39 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3433294253  

I meant the line immediately below: https://github.com/boostorg/release-tools/blob/d27012edbe2516ce8c38798244a153a216ccba95/build_docs/windowsdocs.ps1#L796  
  
> "should be invoked using b2" : b2 calls doc/Jamfile, which in turn will run build_antora.sh.  
  
It does, but this is not part of the contract and should not be relied upon.

---

## Comment 8

> Username: sdarwin  
> Created at: 2025-10-22 17:46:24 UTC  
> Url: https://github.com/boostorg/redis/issues/281#issuecomment-3433502968  

The build_docs directory in release-tools contains convenience scripts to "build docs" locally for individual libraries.  Such as `redis`  .  It's also used when generated "doc previews".    
  
That's not the main superproject build script: ci_boost_release.py.     
  
`ci_boost_release.py` does not call build_docs.
