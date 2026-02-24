# #430 - Failures on Appveyor [Closed]

> Username: pdimov  
> Created at: 2017-11-01 23:05:01 UTC  
> Updated at: 2017-11-02 00:46:19 UTC  
> Closed at: 2017-11-02 00:46:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/430  

While watching the superproject Appveyor, I notice that this `geometry` commit:  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4441-develop  
  
passes the tests, but the two subsequent ones:  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4442-develop  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4443-develop  
  
fail (on all three versions being tested - msvc-12.0, msvc-14.0, msvc-14.1.)

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-11-02 00:28:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/430#issuecomment-341283055  

Thanks about the info. The PR fixing it is ready: https://github.com/boostorg/geometry/pull/429

---

## Comment 2

> Username: pdimov  
> Created at: 2017-11-02 00:46:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/430#issuecomment-341285521  

You're way ahead of me then. :-)
