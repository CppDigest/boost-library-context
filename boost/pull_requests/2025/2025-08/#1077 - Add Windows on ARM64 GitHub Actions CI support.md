# #1077 Add Windows on ARM64 GitHub Actions CI support [Merged]

> Username: vask2108  
> Created at: 2025-08-28 19:24:44 UTC  
> Updated at: 2025-09-26 13:09:50 UTC  
> Merged at: 2025-09-26 13:09:50 UTC  
> Closed at: 2025-09-26 13:09:50 UTC  
> Url: https://github.com/boostorg/boost/pull/1077  

Fixes issue : https://github.com/boostorg/boost/issues/1070  
  
Draft PR for the review.  
  
.gitmodules changes were done to build in my fork. They will be discarded in the due course.

---

## Comment 1

> Username: vask2108  
> Created_at: 2025-09-03 18:27:45 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3250310096  

@pdimov  
I have raised  this PR for Windows Arm64.  Do you know who can review this PR?  My intention is enabled Boost API for Windows ARM64 platform.  
  
Thank you.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-09-03 18:32:21 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3250323118  

Could you please remove the changes to .gitmodules and libs/context from this PR?

---

## Comment 3

> Username: vask2108  
> Created_at: 2025-09-08 20:42:53 UTC  
> Updated_at: 2025-09-08 22:03:24 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3267951065  

@pdimov   
  
Thank you for the response.   
  
The .gitmodules will definitely need to removed. But we need libs/context  changes for the main ci.yml to build for windows arm64  . So first the changes in libs/context needs to be merged to develop2 (when approvers are happy). Without these changes ci.yml work flow with not succeed for windows on ARM64.  
  
Are you recommending create another PR for lib/context changes?   
  
I just created it https://github.com/boostorg/context/pull/315 here. once this is accepted, I can remove the .gitmodules and libs/context changes from this PR , otherwise it may not build, when you enable/approve the work flow to build.  
  
Kindly advise how to progress.  
Thank you.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-09-08 23:28:30 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3268342085  

The `libs/context` submodule tracks the `develop` branch from https://github.com/boostorg/context and is updated automatically by a script. Even if we change it in the PR, the script will just roll back these changes because `libs/context` will no longer correspond to what's in the Context repo.

---

## Comment 5

> Username: vask2108  
> Created_at: 2025-09-09 13:06:46 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3270663836  

@pdimov   
Thank you for the response. Apologies, I have not fully understood unfortunately.  
  
If I remove the .gitmodule and libs/context changes from this branch, how can we ensure that workflow is building correctly?  
If you enable the above  "1 workflow awaiting approval" step, It will look for libs/context changes in the develop (if I am not wrong).  
But my changes to libs/context are not merged yet to develop, so the build fails and it may not be possible to assess whether my changes are actually working or not. ( If I am not wrong)  
  
  
So I thought,   
  
1. First check workflow passes with temporary .gitmodule changes and linking libs/context from my fork and maintainers are happy with the changes.   
2. merge https://github.com/boostorg/context/pull/315, which pushes libs/context changes into the develop branch.  
3. Later I need to update PR by removing temporary .gitmodule changes and removing libs/context as lib/context changes have gone into the develop now.   
4. merge this  PR with ci.yml changes only and It should build without any issue on the develop.  
  
Hope I make sense, If not , please correct me , if this needs to be done in a different way.  
  
These changes are tested by building in my fork and it was working without any issues.  
  
Thank you very much for your patience and help to push these changes.

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-09-09 13:28:30 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3270756018  

OK, I approved the workflow.

---

## Comment 7

> Username: vask2108  
> Created_at: 2025-09-09 14:37:43 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3271035810  

@pdimov   
Thank you.   
I see almost all jobs (including new windows arm64) passing . But cmake-test-posix(ubuntu-latest) is failing, but it appears it not related to my changes to me.   
  
Can you please advice next step of action for me?  
  
Thank you.

---

## Comment 8

> Username: pdimov  
> Created_at: 2025-09-09 15:35:24 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3271261501  

At this point we can only wait for the maintainer of Context to apply the PR.  
  
Maybe you could add Windows jobs to his CI to speed this up? I don't see Windows being tested there at the moment.

---

## Comment 9

> Username: vask2108  
> Created_at: 2025-09-09 16:11:42 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3271391229  

@pdimov  
Thank you. That's correct, there are no windows tests from libs/context CI. There must be some reason.  
Do you know who the maintainer is? I chase them. In the meantime, I can also try and evaluate the writing windows ARM64 CI for libs/context.  
  
Thank you.

---

## Comment 10

> Username: pdimov  
> Created_at: 2025-09-09 17:11:41 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3271596559  

When I rewrote ci.yml in Context, I probably didn't add Windows jobs because I was unable to make them pass in some reasonable amount of time so I gave up.  
  
The maintainer of Context is Oliver Kowalke.  
  
https://github.com/boostorg/boost/blob/009cb0e6389b476703f55055bdb812bb54c3b9a1/libs/maintainers.txt#L38

---

## Comment 11

> Username: pdimov  
> Created_at: 2025-09-09 19:27:21 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3272007765  

I made a PR that adds Windows jobs to Context's ci.yml: https://github.com/boostorg/context/pull/316

---

## Comment 12

> Username: vask2108  
> Created_at: 2025-09-11 15:15:25 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3281350608  

@pdimov   
Thank you .  I have taken changes from https://github.com/boostorg/context/pull/316 and updated my PR https://github.com/boostorg/context/pull/315 with Windows ARM64  and Windows ARM64 tests are passing now waiting for @olk to review it when possible.  
  
Thank you.

---

## Comment 13

> Username: vask2108  
> Created_at: 2025-09-25 16:13:51 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3334924722  

@pdimov  
  
I removed the workarounds now as https://github.com/boostorg/context/pull/315 is merged. If you enable the workflow, It believe, it should pass.  Just in case, If any issues I will look at them tomorrow.  
  
Thank you.

---

## Comment 14

> Username: vask2108  
> Created_at: 2025-09-26 08:10:49 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3337279472  

@pdimov   
Can this PR merged now?  
  
Thank you.

---

## Comment 15

> Username: pdimov  
> Created_at: 2025-09-26 10:46:56 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3338089188  

Looks good. Can you please add the newline at the end, though? Thanks.

---

## Comment 16

> Username: vask2108  
> Created_at: 2025-09-26 10:55:39 UTC  
> Url: https://github.com/boostorg/boost/pull/1077#issuecomment-3338124191  

@pdimov   
New line added. Thank you.

---
