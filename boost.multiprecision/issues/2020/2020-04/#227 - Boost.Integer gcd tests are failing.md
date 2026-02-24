# #227 - Boost.Integer gcd tests are failing [Closed]

> Username: Lastique  
> Created at: 2020-04-28 10:27:23 UTC  
> Updated at: 2020-04-30 09:27:41 UTC  
> Closed at: 2020-04-30 09:27:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/227  

Some recent changes in Boost.Multiprecision develop are causing `gcd` test failures in Boost.Integer. I have not investigated too deep, but with Boost.Multiprecision 1.73 (and the rest of Boost from develop) the tests are passing.  
  
[Here](https://ci.appveyor.com/project/Lastique/integer/builds/32491116/job/4f68o8y8p48ikoxc#L354) is an example of the test failure on MSVC. I can reproduce similar failures locally with gcc 9 as well.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-04-28 18:06:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/227#issuecomment-620767501  

Good catch!  
  
This should fix it once CI has cycled: https://github.com/boostorg/multiprecision/pull/228
