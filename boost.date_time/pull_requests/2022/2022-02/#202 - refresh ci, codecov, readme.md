# #202 refresh ci, codecov, readme [Merged]

> Username: jeking3  
> Created at: 2022-02-11 22:09:04 UTC  
> Updated at: 2022-02-13 11:13:50 UTC  
> Merged at: 2022-02-12 02:14:29 UTC  
> Closed at: 2022-02-12 02:14:29 UTC  
> Url: https://github.com/boostorg/date_time/pull/202  

- align to current boost-ci practice  
- deal with VERY slow test runner in microsec_time_clock test  
- disable sanitize tests - too many issues in boost serialization right now  
- omit unit tests from coverage

---

## Comment 1

> Username: JeffGarland  
> Created_at: 2022-02-11 22:30:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#issuecomment-1036693117  

if we omit unit tests from coverage there won't be any coverage will there?

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2022-02-11 22:30:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/202#pullrequestreview-880740370  

📁 test/posix_time/testmicrosec_time_clock.cpp

```diff
  65 |     std::cout << t2 << std::endl;
  66 | 
  67 |+     // sometimes on very slow systems (github actions) a long time
```

> Username: JeffGarland  
> Created_at: 2022-02-11 22:28:22 UTC  
> Updated_at: 2022-02-11 22:30:43 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#discussion_r805047254  

Should we just consider removing this test from the test set (can still compile it) since it's always been finicky?  I mean it was one thing when I was developing the library and wanted to be sure this property held -- and it would be something to check on a new platform, but I'm not sure as a recurring test it's worth the trouble.

> Username: jeking3  
> Created_at: 2022-02-11 22:38:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#discussion_r805051362  

I tested my fix by suspending it at the right moment and it does skip this, I've seen it correct the issue so I would recommend leaving it as is since it passes now.

> Username: JeffGarland  
> Created_at: 2022-02-11 22:39:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#discussion_r805051872  

roger that

> Username: jeking3  
> Created_at: 2022-02-12 02:09:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#discussion_r805102602  

I take it back... look at this  
  
https://ci.appveyor.com/project/jeking3/date-time-1evbf/builds/42545758/job/6o33narnc03htcs2?fullLog=true#L3633

> Username: jeking3  
> Created_at: 2022-02-12 02:11:33 UTC  
> Updated_at: 2022-02-12 02:11:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#discussion_r805102906  

@JeffGarland I would consider the change I made an improvement, I would like to merge it anyway (CI is *so* expensive on this!) and then if you want to kill the test feel free.  It does seem to fail on the VS2013/2015 test runner specifically, which is interesting.

> Username: JeffGarland  
> Created_at: 2022-02-12 12:45:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#discussion_r805157588  

ok thanks. I'll just leave it alone for now.


---

## Comment 3

> Username: jeking3  
> Created_at: 2022-02-11 22:39:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#issuecomment-1036700346  

Coverage includes the headers.  We omit the unit test code itself from coverage numbers, so all we see is coverage of the library headers.

---

## Comment 4

> Username: jeking3  
> Created_at: 2022-02-11 22:40:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#issuecomment-1036701443  

This also restores Coverity Scan.  
  
There seem to be a number of problems with boost::serialization and ubsan testing so I had to disable it.  I wanted to add exclusions, since there are a number of open issues on serialization that have been there for 3-4 years, but I couldn't find a way to do it in the amount of time I have available.

---

## Comment 5

> Username: jeking3  
> Created_at: 2022-02-11 22:41:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/202#issuecomment-1036702154  

If we want the CI to take less time we can reduce the cxxstd levels that some of the older compilers are using in future changes.  Testing 03,11,13,17,20 is a lot of work.

---
