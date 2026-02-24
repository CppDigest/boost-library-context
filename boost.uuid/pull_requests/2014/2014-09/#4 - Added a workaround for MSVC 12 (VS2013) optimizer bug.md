# #4 Added a workaround for MSVC 12 (VS2013) optimizer bug. [Closed]

> Username: Lastique  
> Created at: 2014-09-27 19:07:37 UTC  
> Updated at: 2015-09-11 18:25:44 UTC  
> Closed at: 2015-09-10 21:17:58 UTC  
> Url: https://github.com/boostorg/uuid/pull/4  

The compiler incorrectly merges unaligned loads with SIMD comparison  
instruction, which causes crashes in Release x64 builds.  
  
https://svn.boost.org/trac/boost/ticket/8509#comment:3  
https://connect.microsoft.com/VisualStudio/feedbackdetail/view/981648#tabs

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-09-09 20:59:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/4#issuecomment-139044210  

I have integrated this fix with uuid on my local 'develop' and my tests for VC++, gcc, and clang look good. So I intend to push my integrated changes to remote 'develop' unless I hear any further objection. Thanks to all who worked on this PR and issued the original bug report.

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-09-10 09:11:24 UTC  
> Url: https://github.com/boostorg/uuid/pull/4#issuecomment-139177725  

The MS Connect bug says the compiler has been fixed in VS2015, but I never got around to verify this. If the bug is really fixed then the condition in the workaround has to be adjusted. Also, the PR seems to have conflicts now. I can update the PR in a few days, if you like.

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-09-10 16:47:09 UTC  
> Url: https://github.com/boostorg/uuid/pull/4#issuecomment-139306945  

You do not have to update the PR. I can change my local copy and test VS2015. After I make my change I will push the change to uuid 'develop'. You can look at it there to make sure everything is working properly if you like.

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-09-10 17:05:31 UTC  
> Url: https://github.com/boostorg/uuid/pull/4#issuecomment-139311179  

I pushed your changes to 'develop'. Take a look at it when you get the chance. I will leave this PR open for a few more days and then close it if everything looks good.

---

## Comment 5

> Username: Lastique  
> Created_at: 2015-09-10 18:45:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/4#issuecomment-139340509  

Looks ok to me. Thanks, Edward.

---

## Comment 6

> Username: eldiener  
> Created_at: 2015-09-10 21:17:58 UTC  
> Url: https://github.com/boostorg/uuid/pull/4#issuecomment-139382005  

OK, I will close this PR and loom at the test results for any possible problems.

---
