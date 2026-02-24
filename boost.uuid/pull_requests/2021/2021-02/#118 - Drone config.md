# #118 Drone config [Closed]

> Username: sdarwin  
> Created at: 2021-02-03 15:22:56 UTC  
> Updated at: 2022-01-25 21:51:29 UTC  
> Closed at: 2022-01-25 21:51:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/118  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: jeking3  
> Created_at: 2021-06-15 16:45:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/118#issuecomment-861660153  

I'm confused; are we using drone or GitHub Actions going forward?  I see PRs for both.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-06-15 17:19:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/118#issuecomment-861684174  

Your decision.  You are welcome to use both, which can provide redundancy and more test coverage.  Sometimes, one of the systems will be slow when the other is fast, so you get faster results.

---

## Comment 3

> Username: jeking3  
> Created_at: 2021-06-15 18:07:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/118#issuecomment-861721689  

I've been away for a good long time due to startups (and just peeking in to see what I need to do for the 1.77 release)... this repo uses Travis CI and Appveyor through boost-ci.  That repo was an attempt to standardize testing throughout boost and it ended up with some adoption.  Rolling out updates was always a problem however as it required individual attention to each repository using it.  
  
Is there a general consensus about which system the project is moving towards?  
  
I'd prefer to see each of these actually run in CI before I merge anything.  I wouldn't mind switching to GitHub Actions at some point.  Not sure if they support different endianness yet, which is important for testing this library.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-06-15 19:10:42 UTC  
> Url: https://github.com/boostorg/uuid/pull/118#issuecomment-861762612  

> I'd prefer to see each of these actually run in CI before I merge anything.  
  
In that case, you might copy the files into a fork in your own github account, and test.

---
