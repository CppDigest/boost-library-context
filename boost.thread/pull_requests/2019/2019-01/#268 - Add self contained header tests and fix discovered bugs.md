# #268 Add self contained header tests and fix discovered bugs [Merged]

> Username: Lastique  
> Created at: 2019-01-19 18:12:39 UTC  
> Updated at: 2019-01-20 21:46:53 UTC  
> Merged at: 2019-01-19 20:04:40 UTC  
> Closed at: 2019-01-19 20:04:40 UTC  
> Url: https://github.com/boostorg/thread/pull/268  

Added tests for self-contained headers.  
  
The new set of tests iterates over Boost.Thread public headers and verifies that  
each header is self-contained, i.e. doesn't have any missing includes and  
contains syntactically correct content. On Windows and Cygwin, a second test  
per each header is generated, which includes the header after windows.h. This  
verifies that the header doesn't have conflicts with Windows SDK and that  
including windows.h does not break platform detection in Boost.Thread.  
  
This set of tests would have detected the bug fixed by https://github.com/boostorg/thread/pull/263.  
  
Fixed bugs discovered by the standalone header tests.  
  
- Added missing includes.  
- Added missing namespace qualification for Boost.Chrono casts.  
- Added typedefs renaming polymorphic lockable wrappers to a more consistent  
  naming scheme. This fixes incorrect name references.  
- Fixed incorrect duration casts in polymorphic lockable wrappers.  
- Renamed upgrade_lockable_adapter to poly_upgrade_lockable_adapter, since  
  upgrade_lockable_adapter already exists and has a different meaning.  
- Use BOOST_THREAD_FUTURE instead of future in executors and task region.  
- Disable serial_executor_cont when executors or future continuations  
  are not enabled.  
- Marked sync_queue_is_closed exception as visible.

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2019-01-19 20:02:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/268#pullrequestreview-194368470  

📁 include/boost/thread/executors/serial_executor_cont.hpp

```diff
  41 |     generic_executor_ref ex_;
  35 |-     future<void> fut_; // protected by mtx_
  42 |+     BOOST_THREAD_FUTURE<void> fut_; // protected by mtx_
```

> Username: viboes  
> Created_at: 2019-01-19 20:02:20 UTC  
> Url: https://github.com/boostorg/thread/pull/268#discussion_r249255824  

This  is not absolutely necessary as this header is supported only after version 3.  
Anyway, it doesn't hurt.

> Username: Lastique  
> Created_at: 2019-01-19 21:04:17 UTC  
> Updated_at: 2019-01-19 21:04:18 UTC  
> Url: https://github.com/boostorg/thread/pull/268#discussion_r249257345  

I did not see this documented anywhere, nor guarded by any macro checks. There must be a macro check so that the header can be included by the tests.

> Username: viboes  
> Created_at: 2019-01-20 11:10:52 UTC  
> Url: https://github.com/boostorg/thread/pull/268#discussion_r249274025  

Unfortunately BOOST_THREAD_PROVIDES_EXECUTORS is not defined in any BOOST_THREAD_VERSION  
```  
Feature                             | Anti-Feature                     | V2   | V3  | V4  
PROVIDES_EXECUTORS | -                                         | NO  | NO | NO  
PROVIDES_FUTURE        | DONT_PROVIDE_FUTURE | NO | YES | YES  
```  
  
https://www.boost.org/doc/libs/master/doc/html/thread/build.html#thread.build.configuration

> Username: Lastique  
> Created_at: 2019-01-20 11:14:10 UTC  
> Url: https://github.com/boostorg/thread/pull/268#discussion_r249274139  

Yes, I noticed that.


---

## Comment 2

> Username: viboes  
> Created_at: 2019-01-19 20:04:32 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455810955  

Thanks for providing those test.

---

## Comment 3

> Username: viboes  
> Created_at: 2019-01-20 07:53:38 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455845066  

Maybe it was not explicitly documented.  
  
But see trying to use executors without version 3 seems to not work  
See  
https://api.travis-ci.org/v3/job/481848302/log.txt

---

## Comment 4

> Username: viboes  
> Created_at: 2019-01-20 08:25:30 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455846813  

To be complete and correct we need to do this check for all the Boost.Thread versions and add something like this on the generated file  
  
```  
#define BOOST_THREAD_VERSION N  
```  
  
In this sway we will see which files is not working for which version.

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-01-20 10:43:18 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455855245  

If you compile all headers with all versions, the tests will fail and thus will be of no use. If you only want to compile headers with the version numbers they support then I'm not sure how to do that since the tests are generated automatically and have no knowledge of the supported versions. Therefore I think all headers must be compatible with all versions, even if it means the headers are empty for unsupported versions, and compiling for the default version (which should be the latest one) is enough.  
  
Also note that the tests already take a long time to compile, which adds to the already long time to complete other tests. Compiling all headers for every version will increase this time N-fold.

---

## Comment 6

> Username: viboes  
> Created_at: 2019-01-20 13:18:07 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455865868  

I don't see why ant test should fails. If each file is protected with respect to the good enablers, non should fail as you have already done for one of the files.  
So yes, all headers must be compatible with all version. This is what I was asking.  
I know that the test will increase the time. I would like to have the test available on demand at least.

---

## Comment 7

> Username: Lastique  
> Created_at: 2019-01-20 14:05:29 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455869229  

Ok. But just to be clear, I don't know what header requires what version, so I'm not planning to add preprocessor checks. I will say though that I'm most interested for the tests to pass in the default version, as this is likely the version most users will use.

---

## Comment 8

> Username: viboes  
> Created_at: 2019-01-20 14:33:23 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455871317  

I'm saying the same as you: "Therefore I think all headers must be compatible with all versions, even if it means the headers are empty for unsupported versions."  
  
However, compiling only for the default version will not check the files are self contained :(  
  
I don't know which versions the people are using, but the default version is quite limited in features.

---

## Comment 9

> Username: viboes  
> Created_at: 2019-01-20 14:33:58 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455871368  

I'm preparing a fix for some of the issues still present on the self consistent files.

---

## Comment 10

> Username: Lastique  
> Created_at: 2019-01-20 16:53:12 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455882656  

> I don't know which versions the people are using, but the default version is quite limited in features.  
  
Well, we're using the default version in other Boost libraries. I'm not aware of anyone setting a non-default version.  
  
If other versions are more useful, why not make it the default?  
  
> I'm preparing a fix for some of the issues still present on the self consistent files.  
  
Ok, good, thanks.

---

## Comment 11

> Username: viboes  
> Created_at: 2019-01-20 17:05:43 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455883592  

> If other versions are more useful, why not make it the default?  
  
backward compatibility :(

---

## Comment 12

> Username: Lastique  
> Created_at: 2019-01-20 18:15:59 UTC  
> Updated_at: 2019-01-20 18:16:17 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455889433  

Those who want a specific API version could (and should) request it explicitly in their project. The default should be the version you recommend people to use in new projects or the projects that are willing to update to a new version. At least, that's how I see it.

---

## Comment 13

> Username: viboes  
> Created_at: 2019-01-20 21:46:53 UTC  
> Url: https://github.com/boostorg/thread/pull/268#issuecomment-455905437  

I proposed long time ago a plan to move to new versions and the people rejected my plan. This is why we have yet so much versions in Boost.Thread.  
  
I did something wrong and I'm tired now to make it better.   
  
If someone wants to make a really new version on separated folders, please go ahead.  
  
If someone want to propose to change the default version for Boost.Thread, please go ahead and propose it on the Boost development mailing list.

---
