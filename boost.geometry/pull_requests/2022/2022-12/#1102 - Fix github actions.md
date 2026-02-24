# #1102 Fix github actions [Merged]

> Username: vissarion  
> Created at: 2022-12-14 15:46:01 UTC  
> Updated at: 2022-12-19 14:53:13 UTC  
> Merged at: 2022-12-19 14:53:09 UTC  
> Closed at: 2022-12-19 14:53:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1102  

Github actions is failing currently. This is due to a change in ubuntu version used by the CI. It used to be 20.04 (see https://github.com/boostorg/geometry/actions/runs/3591991978/jobs/6047189522#step:1:4) but now it is 22.04.1 (see https://github.com/boostorg/geometry/actions/runs/3594906729/jobs/6179253324#step:1:4). This is expected since `ubuntu-latest` can be either 20.04 or 22.04 depending on the runner (see https://github.com/actions/runner-images#ongoing-migrations).   
  
This PR changes the runner from `ubuntu-latest` to `ubuntu-20.04`. If we want to switch to `22.04` we either a) have to fix issues with some old versions of compilers e.g. `clang-3.9` that cannot be installed in 22.04 (`libc6-dev : Breaks: libgcc-7-dev (< 7.5.0-6~) but 7.3.0-16ubuntu3 is to be installed`) or b) decide to stop testing with those compilers or c) use Ubuntu 20.04 for old compilers and 22.04 for new ones.  
  
This PR also fixes [a deprecation warning for set-output CI command](https://github.blog/changelog/2022-10-11-github-actions-deprecating-save-state-and-set-output-commands).

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2022-12-14 16:14:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1102#pullrequestreview-1217770818  

📁 .github/workflows/minimal.yml

```diff
  99 |-           # Required for compilers not available in ubuntu latest
 102 |+           # Required for compilers not available in ubuntu 20.04
 103 |+           sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D05B5
```

> Username: vissarion  
> Created_at: 2022-12-14 16:14:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1102#discussion_r1048673703  

these are needed to get tests passing otherwise I am getting this error:  
```  
Cannot add PPA: 'ppa:~ubuntu-toolchain-r/ubuntu/test'.  
ERROR: '~ubuntu-toolchain-r' user or team does not exist.  
Error: Process completed with exit code 1.  
```  
https://github.com/vissarion/geometry/actions/runs/3696056304/jobs/6259239846#step:6:84


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2022-12-14 16:17:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1102#pullrequestreview-1217779323  

I assume that this is related to the key errors I got. Great, thanks a lot.

---
