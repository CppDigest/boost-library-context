# #51 Reset 'errno' after 'sysconf()' [Merged]

> Username: hammond  
> Created at: 2017-08-23 15:39:58 UTC  
> Updated at: 2018-07-15 09:30:21 UTC  
> Merged at: 2018-06-05 10:11:03 UTC  
> Closed at: 2018-06-05 10:11:03 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51  

In our case (we haven't gotten to the bottom of exactly why), `sysconf()` returns -1 and sets `errno == 38` (ENOSYS). When iterating over a directory, this lead to that error incorrectly being propagated after incrementing past the last directory entry.

---

## Comment 1

> Username: fxb  
> Created_at: 2017-08-28 09:36:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-325308124  

To provide a bit more background on why this fix is needed:  
  
In our case, recent Android NDKs now define `_POSIX_THREAD_SAFE_FUNCTIONS`, and the affected code in Boost.Filesystem now gets built. (See: https://github.com/android-ndk/ndk/issues/11)  
  
Not all platforms support it, which is why a check for `_SC_THREAD_SAFE_FUNCTIONS` using `sysconf` is correct. This will in turn cause `errno` to be set (See: http://man7.org/linux/man-pages/man3/sysconf.3.html). This patch resets `errno` to `0`, since the error is handled gracefully and the code will fallback to use `readdir` instead of its reentrant version `readdir_r`.

---

## Comment 2

> Username: mic-  
> Created_at: 2018-03-22 14:33:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-375327783  

I can confirm that this patch fixes an issue I had where incrementing a `directory_iterator` would fail on a Samsung Galaxy S4 running Android 5.0.1 when building with NDK r16b.  
  
The unpatched code works fine when built with NDK r12b, since `_POSIX_THREAD_SAFE_FUNCTIONS` was undefined in that NDK release. The unpatched code would also work fine on a Samsung Galaxy S7 running Android 7.0, since the `_SC_THREAD_SAFE_FUNCTIONS` runtime check succeeds on that device.

---

## Comment 3

> Username: alexeikh  
> Created_at: 2018-05-18 15:53:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-390251203  

I am confirming that this patch fixes bug #72 for me on Sony Xperia Z5 phone running Android 5.1.1 when building a test app with Android NDK r16b.

---

## Comment 4

> Username: alexeikh  
> Created_at: 2018-05-30 10:51:29 UTC  
> Updated_at: 2018-05-30 11:12:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-393117562  

Non-maintainer review of the patch. Hopefully will help the maintainers to spend less time on their official review.  
  
The patch is tiny, straightforward, very understandable. It has zero risk of introducing a regression. The patch can benefit from a code comment, explaining why `errno` needs to be reset 2 times. Similar to @fxb's explanation above.  
  
Even if the patch can be improved, I think it is already good and can be merged in.

---

## Comment 5

> Username: alexeikh  
> Created_at: 2018-06-05 09:14:18 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-394639739  

Dear maintainers, @Beman and @pdimov , can you please merge this tiny patch? It solves quite a major issue and has zero regression risk. Without this patch, Boost.Filesystem is basically unusable on Android. With this patch and the already-merged #69 , the next release of Boost.Filesystem can be "Android-ready".

---

## Comment 6

> Username: rcdailey  
> Created_at: 2018-07-14 23:55:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-405057137  

Which version of boost will this fix be available in? 1.67?

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-07-15 09:27:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-405078674  

1.68, due in a few days.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-07-15 09:30:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/51#issuecomment-405078812  

Make that a few weeks, 1.68 is scheduled for Aug 1st. :-)

---
