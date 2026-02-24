# #17 Fixes for #7419 - Support multiple calls to framework::init() allowing wrappers to support running tests using test tools in full systems [Open]

> Username: ja11sop  
> Created at: 2014-10-18 01:04:22 UTC  
> Updated at: 2024-05-31 08:43:29 UTC  
> Url: https://github.com/boostorg/test/pull/17  

Pull request for https://svn.boost.org/trac/boost/ticket/7419 allowing boost.test to be used in production systems in a test mode. In other words test can be configured using non-conflicting command-line options and be executed in arbitrary code. Please see the referenced ticket #7419 for full details which explains the use case. Our production code relies on this and the related patches/pull requests #15 and #16. As a set they enable powerful use of boost test by making the test tools available in non-test programs and allow sophisticated tools to be built off boost.test's output.  
  
We've been using these patches for over 2 years painfully applying them to boost test for each release. They are small and unobtrusive and now that we're all comfy in git I think it's time to get them added so others can benefit and boost.test is no longer broken when used with boost.timer.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2017-03-18 13:43:46 UTC  
> Url: https://github.com/boostorg/test/pull/17#issuecomment-287547211  

Please let me know if this is still of some interest for you. It would be good if you rebase on develop.

---

## Comment 2

> Username: ja11sop  
> Created_at: 2018-06-27 22:13:18 UTC  
> Url: https://github.com/boostorg/test/pull/17#issuecomment-400846157  

This is still of interest to me - I see most of the code relating to this has changed - I'll see what I can do

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2018-06-27 22:32:27 UTC  
> Url: https://github.com/boostorg/test/pull/17#issuecomment-400850554  

I would suggest that we close this PR and start a new one, based on the latest develop. We have for instance a custom logger facility that may be used as is, and it is not clear to me what is needed from the init phase of the test setup. For instance the loggers may not need to be set up again at each iteration.  
  
Let me know how you want to proceed

---

## Comment 4

> Username: ja11sop  
> Created_at: 2018-10-20 23:14:28 UTC  
> Url: https://github.com/boostorg/test/pull/17#issuecomment-431625238  

I'm looking at this now - it's still broken but the fix might be minor.

---

## Comment 5

> Username: ja11sop  
> Created_at: 2018-10-21 19:38:14 UTC  
> Url: https://github.com/boostorg/test/pull/17#issuecomment-431697505  

The current PR has no conflicts and supports the use case of calling init() on the framework more than once which we use to allow us to launch test suites as a special mode in production executables (sort of a self validating test run or runs). We need to be able to put the boost test command line parameters in a boost.test namespace so we don't conflict with actual application command line options.

---
