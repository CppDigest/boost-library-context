# #324 Update lcov coverage [Closed]

> Username: sdarwin  
> Created at: 2021-04-28 21:30:45 UTC  
> Updated at: 2021-05-06 17:40:30 UTC  
> Closed at: 2021-05-06 17:01:27 UTC  
> Url: https://github.com/boostorg/histogram/pull/324  

Hi,  
  
Some info about the changes:  
- upgrade lcov from 1.14 to 1.15  
- adds support for lcov with gcc-9 and gcc-10.  However, lcov 1.15 processing gcc-9 and 10 is rather slow.  
- gcov ideally shouldn't be hardcoded to version 8. Rather, match the gcc version. Thus, GCOVTOOL='gcov-4.6' for gcc-4.6, or GCOVTOOL='gcov-6' for gcc-6.  
- btw, the new cov.yml file was based on https://github.com/boostorg/boost-ci/blob/master/ci.yml, which is a generic GHA file that pdimov wrote to use with any boost library.  
  
> I also want to generate coverage data offline.  
  
That should be fairly simple, by running the same CI scripts on a local machine.   I will test it out, and send over the steps.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2021-04-29 20:28:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/324#issuecomment-829574074  

The most recent commit includes "tools/cov_offline.sh".    Running this standalone script will -   
- checkout the repo (if it hasn't already been done)   
- build boost  
- build the library  
- run lcov, generating coverage.info  
- optionally upload the file to coveralls.io

---

## Comment 2

> Username: HDembinski  
> Created_at: 2021-05-06 15:57:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/324#issuecomment-833637431  

Cool, thanks, sorry for the late reply.  
  
> gcov ideally shouldn't be hardcoded to version 8. Rather, match the gcc version. Thus, GCOVTOOL='gcov-4.6' for gcc-4.6, or GCOVTOOL='gcov-6' for gcc-6.  
  
Sure, the problem is that no other version of gcc worked.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2021-05-06 17:01:27 UTC  
> Url: https://github.com/boostorg/histogram/pull/324#issuecomment-833689068  

After careful reading I am going to reject this. This is a very invasive patch that changes at lot of things that do not need changing.  
  
The salient change seems to be to upgrade to lcov-1.15.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-05-06 17:40:29 UTC  
> Url: https://github.com/boostorg/histogram/pull/324#issuecomment-833725174  

Hi Hans,  
  
That's fine.  The intention was not to rewrite so many aspects of the file, but rather to find a way to quickly implement a "matrix" style build, because you were concerned about multiple different newer gcc versions. I copied an existing ci.yml file,  standard boilerplate, and known to be stable.  It has a matrix.  Then, modified it for lcov.     
  
You are right that the main fix is lcov-1.15.      
  
Keep in mind that point about the GCOV version matching.   It was done here with a variable in the matrix, instead of hardcoded.  
  
You had asked about offline testing, outside of CI.    The file tools/cov_offline.sh in this pull request does that.  So, you could merge that script separately.    The only concern .... is line 45 of tools/cov.sh   
  
```  
$LCOV --base-directory `pwd`/../../ \  
```  
Notice this sets the base directory to be boost-root.  
  
Which reminds me...  testing all versions lcov with gcc, including gcc-9 and gcc-10, I believe there was an issue with the base-directory, and it had to be changed, as shown.   By staying with your old coverage files, that may possibly remain an open issue still to be resolved.  
  
The new cov.yml was based on https://github.com/boostorg/core/blob/develop/.github/workflows/ci.yml , and pdimov is doing a great job of keeping that GHA configuration in shape.   Standardizing on his work for github actions isn't a bad idea.

---
