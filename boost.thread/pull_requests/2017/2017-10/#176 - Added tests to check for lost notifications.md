# #176 Added tests to check for lost notifications. [Merged]

> Username: austin-beer  
> Created at: 2017-10-10 18:49:36 UTC  
> Updated at: 2017-10-11 15:46:09 UTC  
> Merged at: 2017-10-11 06:31:57 UTC  
> Closed at: 2017-10-11 06:31:57 UTC  
> Url: https://github.com/boostorg/thread/pull/176  

I finally got some time to work on this again. Here are the tests I've put together for the lost notification issue. They fail with the current `feature/timespec_clocks` branch, but succeed with the current `develop` branch and with the fixes I've made in `feature/timespec_clocks_pr14` (https://github.com/boostorg/thread/pull/170).

---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-11 06:24:40 UTC  
> Url: https://github.com/boostorg/thread/pull/176#issuecomment-335695579  

Austin, as I said to you, I'm busy until the 16h.   
  
Hrr, the develop branch is broken again.  
  
I'll see what I can do.

---

## Comment 2

> Username: viboes  
> Created_at: 2017-10-11 06:30:56 UTC  
> Url: https://github.com/boostorg/thread/pull/176#issuecomment-335696660  

As this PR contains only test, I have decided to merge them and we will see. I'm confident you have done the tests, and if something don't works we will fix them.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-10-11 06:55:07 UTC  
> Url: https://github.com/boostorg/thread/pull/176#issuecomment-335700933  

I'll merge develop into time_spec branch. An then I will merge #170.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-10-11 06:58:11 UTC  
> Url: https://github.com/boostorg/thread/pull/176#issuecomment-335701539  

Hrr, I believe this was for develop branch :(

---

## Comment 5

> Username: viboes  
> Created_at: 2017-10-11 07:19:04 UTC  
> Url: https://github.com/boostorg/thread/pull/176#issuecomment-335708337  

Austin, I've merged the thread develop branch into timespec_clocks.  
Hopping this will work on the CI.  
  
If it is the case you will need to do the same, taking care of merging Boost.Build and Boost root as well :(  
  
Unfortunately I did some changes in develop pthread condition_variable (check -> unlock_if_locked) that could raise some conflicts in your branch :(  
  
Apologies for all this breaking changes.

---

## Comment 6

> Username: viboes  
> Created_at: 2017-10-11 11:20:42 UTC  
> Url: https://github.com/boostorg/thread/pull/176#issuecomment-335778152  

It is working now https://travis-ci.org/boostorg/thread/builds/286391606  
If you don't reach to do the update, I'll try to merge your PR myself and fix the possible issues.

---
