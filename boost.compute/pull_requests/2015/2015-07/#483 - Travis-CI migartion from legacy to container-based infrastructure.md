# #483 Travis-CI migartion from legacy to container-based infrastructure [Closed]

> Username: jszuppe  
> Created at: 2015-07-18 19:09:33 UTC  
> Updated at: 2016-05-01 11:21:05 UTC  
> Closed at: 2016-02-26 21:02:18 UTC  
> Url: https://github.com/boostorg/compute/pull/483  

This pull request addresses issue https://github.com/boostorg/compute/issues/481.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-07-18 19:39:42 UTC  
> Updated_at: 2015-07-18 19:52:30 UTC  
> Url: https://github.com/boostorg/compute/pull/483#issuecomment-122587925  

There's a problem, we use packages that are not white-listed on Travis CI:  
  
fglrx=2:8.960-0ubuntu1, libboost-chrono1.48-dev, libboost-date-time1.48-dev, libboost-test1.48-dev,  
libboost-system1.48-dev, libboost-filesystem1.48-dev, libboost-timer1.48-dev,  
libboost-program-options1.48-dev, libboost-thread1.48-dev  
- fglrx (without `=2:8.960-0ubuntu1`) is already pending for being white-listed  (support issue https://github.com/travis-ci/travis-ci/issues/4228)  
- libboost-XXX1.48-dev can be replaced with 1.55 versions or requested for white-list on Travis CI (support issue https://github.com/travis-ci/travis-ci/issues/4438)

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-07-20 05:05:46 UTC  
> Url: https://github.com/boostorg/compute/pull/483#issuecomment-122763857  

Thanks for looking into this! Yeah, looks like we'll have to wait for at least the `fglrx` packages are available.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2015-09-06 10:09:07 UTC  
> Url: https://github.com/boostorg/compute/pull/483#issuecomment-138062115  

> As the offending bits were found in binary files, I don't know if we can enable this. Sorry.  
> - BanzaiMan in https://github.com/travis-ci/apt-package-whitelist/issues/383  
  
It seems that fglrx won't be white-listed.

---
