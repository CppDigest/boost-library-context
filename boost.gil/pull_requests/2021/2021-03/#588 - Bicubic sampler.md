# #588 Bicubic sampler [Open]

> Username: Scramjet911  
> Created at: 2021-03-30 09:11:18 UTC  
> Updated at: 2021-04-15 14:55:37 UTC  
> Url: https://github.com/boostorg/gil/pull/588  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
This PR adds a bicubic sampler in the gil image resizing algorithms.  
It also adds another `static_for_each()` method with 5 sources for easier manipulation of the sampler.   
The overflow of the pixel values are checked inside the `cubic_interpolate` function. This is not optimum, but doing it after all cubic calculations is causing overflow errors in the output.   
I would like to know of better ways to do this..  
  
Also I haven't written testcases for the new `static_for_each()` method.  
  
Closes #576   
  
### References  
  
- [Bicubic Sampler](https://www.paulinternet.nl/?page=bicubic)  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] (Maybe) change overflow check outside the `cubic_interpolation` function  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-03-30 10:08:03 UTC  
> Updated_at: 2021-03-30 10:52:18 UTC  
> Url: https://github.com/boostorg/gil/pull/588#issuecomment-810094777  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/588?src=pr&el=h1) Report  
> Merging [#588](https://codecov.io/gh/boostorg/gil/pull/588?src=pr&el=desc) (b81d3cc) into [develop](https://codecov.io/gh/boostorg/gil/commit/bc3a6c0db9dd0b66f4842bfe55b39bc0119e4639?el=desc) (bc3a6c0) will **increase** coverage by `0.59%`.  
> The diff coverage is `99.32%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #588      +/-   ##  
===========================================  
+ Coverage    78.72%   79.31%   +0.59%       
===========================================  
  Files          118      118                
  Lines         5034     5183     +149       
===========================================  
+ Hits          3963     4111     +148       
- Misses        1071     1072       +1       
```

---

## Comment 2

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-30 10:27:33 UTC  
> Url: https://github.com/boostorg/gil/pull/588#issuecomment-810107847  

@Scramjet911  Can you kindly mention how do these tests work? What is the function Boost.test.Eq? I wish to learn about these tests

---

## Comment 3

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-30 10:33:07 UTC  
> Url: https://github.com/boostorg/gil/pull/588#issuecomment-810111302  

Also,how do you design the test cases?

---

## Comment 4

> Username: Scramjet911  
> Created_at: 2021-03-30 10:34:19 UTC  
> Url: https://github.com/boostorg/gil/pull/588#issuecomment-810112099  

> @Scramjet911  Can you kindly mention how do these tests work? What is the function Boost.test.Eq? I wish to learn about these tests  
>   
  
Boost.test.eq checks for the equality of the pixel values. The tests are basically instantiating an image with some pixels and then resizing it and testing the values of the pixels of the resized image.

---

## Comment 5

> Username: lpranam  
> Created_at: 2021-03-30 10:38:16 UTC  
> Updated_at: 2021-03-30 10:38:43 UTC  
> Url: https://github.com/boostorg/gil/pull/588#issuecomment-810114622  

@Sayan-Chaudhuri a simple google search will help you "boost tests" or "boost lightweight test"  
  
https://www.boost.org/doc/libs/1_75_0/libs/core/doc/html/core/lightweight_test.html

---

## Comment 6

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-30 10:40:51 UTC  
> Url: https://github.com/boostorg/gil/pull/588#issuecomment-810116109  

@lpranam Thanks.

---
