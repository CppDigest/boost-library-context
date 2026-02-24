# #52 operations: Don't set _FILE_OFFSET_BITS for older Android versions [Closed]

> Username: tiwoc  
> Created at: 2017-09-04 09:26:19 UTC  
> Updated at: 2018-09-06 09:06:48 UTC  
> Closed at: 2018-09-06 01:37:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/52  

The build was broken when building for Android < 7.0 with the latest  
Android NDK (r15c). With earlier NDKs, the build succeeded, but silently  
used 32b offsets.

---

## Comment 1

> Username: KayEss  
> Created_at: 2017-11-30 11:17:57 UTC  
> Updated_at: 2017-11-30 11:19:35 UTC  
> Url: https://github.com/boostorg/filesystem/pull/52#issuecomment-348159479  

I'd just forked the project to make a very similar change. Would be really nice to see this merged.  
  
Here is a thread discussing this from the Android NDK side: https://github.com/android-ndk/ndk/issues/516  
  
Although they believe that 1.64 is good, 1.65.1 certainly doesn't work

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-03-01 23:40:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/52#issuecomment-369769991  

See also https://github.com/boostorg/filesystem/pull/67.

---

## Comment 3

> Username: alexeikh  
> Created_at: 2018-05-11 08:35:02 UTC  
> Updated_at: 2018-09-06 09:06:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/52#issuecomment-388299225  

Now when #69 has been merged, this pull request is not needed any more and can be closed, I think.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-09-06 01:37:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/52#issuecomment-418935172  

Leave a comment

---
