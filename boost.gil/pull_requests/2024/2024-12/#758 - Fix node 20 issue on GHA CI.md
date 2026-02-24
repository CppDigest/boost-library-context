# #758 Fix node 20 issue on GHA CI [Merged]

> Username: Flamefire  
> Created at: 2024-12-07 18:46:02 UTC  
> Updated at: 2024-12-08 19:53:25 UTC  
> Merged at: 2024-12-08 19:29:30 UTC  
> Closed at: 2024-12-08 19:29:30 UTC  
> Url: https://github.com/boostorg/gil/pull/758  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2024-12-07 19:38:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/758#pullrequestreview-2486675582  

Thank you very much!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-12-07 21:44:25 UTC  
> Updated_at: 2024-12-08 19:29:37 UTC  
> Url: https://github.com/boostorg/gil/pull/758#issuecomment-2525319671  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/758?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 82.46%. Comparing base [(`322c4e2`)](https://app.codecov.io/gh/boostorg/gil/commit/322c4e2e191458383db0f2873dd3301f05a7d137?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`049a06f`)](https://app.codecov.io/gh/boostorg/gil/commit/049a06f3ce35be544ba76d35e5400c59dbb058b4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 13 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #758      +/-   ##  
===========================================  
+ Coverage    82.12%   82.46%   +0.34%       
===========================================  
  Files          117      117                
  Lines         5355     5727     +372       
===========================================  
+ Hits          4398     4723     +325       
- Misses         957     1004      +47       
```  
  
</details>

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2024-12-08 19:29:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/758#pullrequestreview-2487250561  

Thanks a lot @Flamefire   
  
The failing Mac OS job seems unrelated to the Node fixes:  
  
```  
testing.capture-output bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.run  
/bin/sh: line 4: 17731 Abort trap: 6           "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image" > "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
dyld[17731]: Symbol not found: __ZNSt3__13pmr20get_default_resourceEv  
  Referenced from: <DB80F50C-E469-3DF2-A4D7-AD21DD4B429C> /Users/runner/work/gil/boost-root/bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image  
  Expected in:     <8CAC377D-FCC3-34DD-8FA6-C76EBB4D68C9> /usr/lib/libc++.1.dylib  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
  
    DYLD_LIBRARY_PATH="/Users/runner/work/gil/boost-root/bin.v2/libs/atomic/build/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden:/Users/runner/work/gil/boost-root/bin.v2/libs/filesystem/build/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden:/Users/runner/work/gil/boost-root/bin.v2/libs/system/build/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden:$DYLD_LIBRARY_PATH"  
export DYLD_LIBRARY_PATH  
  
     "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image"   > "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.output"  
    echo EXIT STATUS: $status >> "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.output"  
    if test $status -eq 0 ; then  
        cp "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.output" "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.run" >/dev/null  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image.run...  
```  
  
So, I'm merging this PR as successful.

---
