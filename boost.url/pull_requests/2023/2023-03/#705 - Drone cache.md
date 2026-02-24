# #705 Drone cache [Merged]

> Username: alandefreitas  
> Created at: 2023-03-03 22:33:16 UTC  
> Updated at: 2023-07-24 14:36:30 UTC  
> Merged at: 2023-03-07 04:09:10 UTC  
> Closed at: 2023-03-07 04:09:10 UTC  
> Url: https://github.com/boostorg/url/pull/705  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-03 22:51:18 UTC  
> Updated_at: 2023-03-07 03:42:11 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454230468  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/705?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#705](https://codecov.io/gh/boostorg/url/pull/705?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5d9c3cb) into [develop](https://codecov.io/gh/boostorg/url/commit/06697d4dbfad87f5b8ce363202d06317c6488a08?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (06697d4) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 5d9c3cb differs from pull request most recent head c78e14b. Consider uploading reports for the commit c78e14b to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/705/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/705?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #705   +/-   ##  
========================================  
  Coverage    97.13%   97.13%             
========================================  
  Files          154      154             
  Lines         8440     8440             
========================================  
  Hits          8198     8198             
  Misses         242      242             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/705?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/705?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [06697d4...c78e14b](https://codecov.io/gh/boostorg/url/pull/705?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-03-03 22:51:20 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454230508  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2023-03-03 22:51:38 UTC  
> Updated_at: 2023-03-03 22:53:06 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454230744  

@sdarwin   
  
Still failing to upload intermittently: https://drone.cpp.al/boostorg/url/2102/1/4  
  
```  
level=error name=drone-cache ts=2023-03-03T22:45:49.464797598Z caller=main.go:628   
err="[IMPORTANT] build cache, rebuild failed, upload from <cache> to   
<url/boostorg-url-develop-amd64-cppalliance-droneubuntu2204-1/cache>, rebuilder   
rebuild put file, upload file, pipe reader failed, storage backend put failure, put the object,   
NoCredentialProviders: no valid providers in chain. Deprecated.\n\tFor verbose  
 messaging see aws.Config.CredentialsChainVerboseErrors\n"  
```  
  
I'm failing to test the path that actually uses the cache because it's never uploaded.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-03-03 23:07:28 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454244660  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: sdarwin  
> Created_at: 2023-03-03 23:08:49 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454245743  

@alandefreitas the API keys were enabled on the CPPAlliance account only.     Ok, now added to boostorg.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2023-03-03 23:17:08 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454252211  

Don't merge the pr yet. I have at least one possible change planned, switch "mount" to "mounts", a list.

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2023-03-03 23:23:27 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454256557  

> the API keys were enabled on the CPPAlliance account only  
  
Could you enable it for `alandefreitas` too so I can test these kinds of changes?  
  
> Don't merge the pr yet.   
  
I merged the one in boostservertech already. I was replicating it for boost.url because `drone.sh` needs some adjustments and boost.url ends up being the "source of truth" for the generator.  
  
>  switch "mount" to "mounts", a list.  
  
Ok. Let me know if I have to change anything in the generator. I guess the environment variable might need to change to a comma-separated list or something.

---

## Comment 8

> Username: sdarwin  
> Created_at: 2023-03-03 23:29:28 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1454260544  

Enabled on `alandefreitas` with organization secret.

---

## Comment 9

> Username: sdarwin  
> Created_at: 2023-03-06 17:30:41 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1456589854  

@alandefreitas - 'drone_cache_mount' has been modified to support a comma separated list of mount points. The name stayed the same since the plugin itself uses the terminology "mount".   
  
When using one directory, no change is required.  With multiple directories, the new feature is available.

---

## Comment 10

> Username: alandefreitas  
> Created_at: 2023-03-06 18:09:23 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1456681075  

`restore-cache` keeps failing:  
  
```  
level=error name=drone-cache ts=2023-03-06T17:57:46.332474138Z caller=main.go:628 err="[IMPORTANT] restore cache, restore failed, download from <url/boostorg-url-develop-amd64-cppalliance-droneubuntu2204-1/cache> to <cache>, extract files from downloaded archive, pipe reader failed, tar reader <get file from storage backend, pipe writer failed, storage backend get failure, get the object, NoSuchKey: The specified key does not exist.\n\tstatus code: 404, request id: WWD8M384281977MV, host id: HXBfH/q9t1wVk/k43Sa+1n9uzlyoUGHiV4M07x8UpLwEji+cqwjGccNLsLIOk9hC8/fwjrSMHaI=>, archive not readable\n"  
```  
  
I'm not sure the cached path of the script is working because `restore-cache` always fails so there's never a `cache` directory in the `Everything` step.

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-03-06 18:21:05 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1456709671  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: sdarwin  
> Created_at: 2023-03-06 19:20:19 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1456813580  

> restore-cache keeps failing  
  
That's because the previous attempts, which were last week, did not yet have the api key installed.  So there was no cache to restore from.    
  
Restarting jobs today, now, shows "cache restored" took=8.833555287s.

---

## Comment 13

> Username: sdarwin  
> Created_at: 2023-03-06 23:04:46 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457182932  

@alandefreitas When I tried a similar command locally, xargs needed a --null flag:  
  
```  
xargs -I{} --null  
```

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-03-06 23:24:59 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457204873  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-03-07 00:04:14 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457242486  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-03-07 00:14:53 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457254521  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-03-07 00:44:23 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457286937  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-03-07 00:56:35 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457297365  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-03-07 01:29:30 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457336570  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2023-03-07 02:04:43 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457382682  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-03-07 02:16:48 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457395852  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2023-03-07 02:31:07 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457407546  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2023-03-07 03:05:04 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457437928  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2023-03-07 03:29:22 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457458042  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2023-03-07 03:58:01 UTC  
> Url: https://github.com/boostorg/url/pull/705#issuecomment-1457483219  

GCOVR code coverage report [https://705.url.prtest.cppalliance.org/gcovr/index.html](https://705.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://705.url.prtest.cppalliance.org/genhtml/index.html](https://705.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://705.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
