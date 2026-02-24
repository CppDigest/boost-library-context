# #761 Self-hosted github actions runners [Closed]

> Username: sdarwin  
> Created at: 2023-06-28 15:46:49 UTC  
> Updated at: 2023-07-22 18:21:59 UTC  
> Closed at: 2023-07-22 18:21:59 UTC  
> Url: https://github.com/boostorg/url/pull/761  

This is the first pull request using the new runners and so it is a debug/validation phase also.  
  
In other testing of URL there were a couple of errors on Windows.  Potential solutions are:  
  
1. fix a bug in the URL codebase  
2. Or, if you have some idea why the error happens, suggest an update to the Windows images.  I will add new packages to the Windows AMIs  
3. Or, if it remains a mystery, set the runner label to 'windows-2022-no-aws' , which will cause that job to always run on a GitHub-hosted runner.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2023-06-28 15:59:53 UTC  
> Updated_at: 2023-06-28 16:00:12 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1611698587  

Wow! That's amazing!  
  
What is the relationship between the images in self-hosted runners and the github runners? Do they have similar packages, etc?  
  
What are the criteria in terms of permissions? Are the criteria being from github.com/boostorg or github.com/cppalliance?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2023-06-28 16:09:07 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1611713492  

The self-hosted runners were approximately based on the Drone docker images which have a collection of packages allowing boost C++ libraries to build but not a huge number of other things, npm packages, Visual Basic, and so on.  https://github.com/cppalliance/terraform-aws-github-runner/tree/cppal/images  
  
Permissions are controlled by the GitHub App, meaning Peter or Glen modify the installation of the GitHub App to apply to a set of repositories.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-06-28 16:09:58 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1611715079  

GCOVR code coverage report [https://761.url.prtest.cppalliance.org/gcovr/index.html](https://761.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://761.url.prtest.cppalliance.org/genhtml/index.html](https://761.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://761.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://761.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2023-06-28 16:10:29 UTC  
> Updated_at: 2023-07-03 21:32:21 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1611715881  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/761?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#761](https://app.codecov.io/gh/boostorg/url/pull/761?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (07e5b9e) into [develop](https://app.codecov.io/gh/boostorg/url/commit/2bae458a79365883d2bee5f5b00d30e99dd5020b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2bae458) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/761/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/761?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #761   +/-   ##  
========================================  
  Coverage    97.25%   97.25%             
========================================  
  Files          155      155             
  Lines         8518     8518             
========================================  
  Hits          8284     8284             
  Misses         234      234             
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/761/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/761?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/761?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2bae458...07e5b9e](https://app.codecov.io/gh/boostorg/url/pull/761?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2023-06-28 17:00:21 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1611780173  

> The self-hosted runners were approximately based on the Drone docker images which have a collection of packages allowing boost C++ libraries to build but not a huge number of other things, npm packages, Visual Basic, and so on. https://github.com/cppalliance/terraform-aws-github-runner/tree/cppal/images  
  
Nice. That's even better.  
  
> Permissions are controlled by the GitHub App, meaning Peter or Glen modify the installation of the GitHub App to apply to a set of repositories  
  
What app? That means anyone who installs the apps can use these runners? I'm asking because I'm developing the cpp-actions we're using under `https://github.com/alandefreitas/cpp-actions/actions`.  
  
On a side note, I think the biggest problem with public runners is how long they take to become available for boostorg rather than how slow they are. I believe that's why runner selection using `ubuntu-latest` is not running and is blocking everything else from happening. It kind of contradicts the value of the self-hosted runners because runners for other jobs will usually be available when runners for `runner-selection` are available. You probably already noticed by now anyway but it's worth mentioning because you might not be watching the jobs.  
  
On the other hand, I really like the idea of the runner-selection job. It's pretty elegant.   
  
I think we don't need `needs: [cpp-matrix,runner-selection]` for the `build` jobs, because `cpp-matrix` already depends on `runner-selection` and Github can figure this out.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-06-28 17:29:22 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1611816717  

GCOVR code coverage report [https://761.url.prtest2.cppalliance.org/gcovr/index.html](https://761.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://761.url.prtest2.cppalliance.org/genhtml/index.html](https://761.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://761.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://761.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: sdarwin  
> Created_at: 2023-06-28 17:41:05 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1611831558  

@alandefreitas   
  
Documentation about the runners: https://github.com/cppalliance/githubactions  
  
> that's why runner selection using ubuntu-latest is not running   
  
There are pros and cons with setting that initial job to [ self-hosted, linux, x64, ubuntu-latest-aws ], which will cause it to use self-hosted runners.  
- If github is being responsive, and everything is pointed to using github,  a self-hosted runner at the first step causes a delay.    
- If github is slow, and everything is pointed to AWS, then it's faster to set self-hosted.    
  
I have changed the initial job to "[ self-hosted, linux, x64, ubuntu-latest-aws ]".  
  
A "GitHub App" is not an app, it's a collection of permissions in the GUI.  https://github.com/apps/terraform-aws-runners-boost        requiring an organization admin to install.    
  
> I think we don't need "needs: [cpp-matrix,runner-selection]"  
  
Not sure if it needs the full "needs" or not.   You're welcome to experiment and modify that line.

---

## Comment 8

> Username: sdarwin  
> Created_at: 2023-06-28 22:49:28 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1612212433  

A few windows jobs failed as expected.  Let me know if you have any ideas about fixes. "Allow edits by maintainers" is enabled, so you may push to the branch.   Or I will.   Whatever changes are made, the alternate "needs" syntax could be tested, at the same time.

---

## Comment 9

> Username: sdarwin  
> Created_at: 2023-07-03 21:12:06 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1619155901  

> I think we don't need needs: [cpp-matrix,runner-selection] for the build jobs, because cpp-matrix already depends on runner-selection and Github can figure this out.  
  
Not the case. The `needs:` directive should stay in the workflow file, otherwise an error:   
  
```  
Annotations  
2 errors  
CI: .github#L1  
Error when evaluating 'runs-on' for job 'changelog'. .github/workflows/ci.yml (Line: 271, Col: 14): Error parsing fromJson,.github/workflows/ci.yml (Line: 271, Col: 14): Error reading JToken from JsonReader. Path '', line 0, position 0.,.github/workflows/ci.yml (Line: 271, Col: 14): Unexpected value ''  
CI: .github#L1  
Error when evaluating 'runs-on' for job 'build'. .github/workflows/ci.yml (Line: 82, Col: 14): Error parsing fromJson,.github/workflows/ci.yml (Line: 82, Col: 14): Error reading JToken from JsonReader. Path '', line 0, position 0.,.github/workflows/ci.yml (Line: 82, Col: 14): Unexpected value ''  
```

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-07-03 21:28:32 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1619168949  

GCOVR code coverage report [https://761.url.prtest2.cppalliance.org/gcovr/index.html](https://761.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://761.url.prtest2.cppalliance.org/genhtml/index.html](https://761.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://761.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://761.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: alandefreitas  
> Created_at: 2023-07-03 22:29:06 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1619216869  

> Not the case. The needs: directive should stay in the workflow file, otherwise an error:  
  
Oh... I think I wasn't clear about that.  
  
I meant we don't need `needs: [cpp-matrix,runner-selection]` because `build` only needs `needs: [cpp-matrix]`.   
  
I didn't mean that completely removing the `needs` directive and not declaring the requirements at all is something that could work.

---

## Comment 12

> Username: sdarwin  
> Created_at: 2023-07-03 22:53:04 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1619232377  

The test was `needs: [cpp-matrix]`, which resulted in the above mentioned errors.  
  
I hadn't even noticed it before, but the generated "C++ Test Matrix" is really cool!   All the output that appears on the Github Actions page of test results.  Very nice.

---

## Comment 13

> Username: alandefreitas  
> Created_at: 2023-07-22 18:21:58 UTC  
> Url: https://github.com/boostorg/url/pull/761#issuecomment-1646644401  

Merged as part of #773

---
