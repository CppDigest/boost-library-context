# #127 More tests on CI [Closed]

> Username: Flamefire  
> Created at: 2018-10-14 07:49:34 UTC  
> Updated at: 2024-12-09 10:29:15 UTC  
> Closed at: 2018-11-17 19:29:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/127  

This builds feature branches on appveyor as it does for travis -> consistency  
  
It further adds more test runs for release builds, so that is properly tested. This is required as especially with Boost.Serialization optimization may remove classes that are required to stay although they are not referenced. Those visibility macros are in place to allow this, these tests should check them.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-17 13:07:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/127#issuecomment-430620563  

Splitting out debug and release to their own job isn't efficient.  See my recent merged PRs into assign and logic for .travis.yml; these combine things into one build job per compiler, which is more efficient.  You can use multiple cxxstd and variant in the same build job and this avoids the overhead of environment setup.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2018-10-17 13:59:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/127#issuecomment-430639821  

You are right: 90b8ea0f3d0a836b9957b373c4b7154a89e74ac0  
  
I think it would also be a good idea to combine static/shared builds? It was explicitely split probably to have failures show where they occurred on top-level, but again: More overhead.

---

## Comment 3

> Username: robertramey  
> Created_at: 2018-11-17 19:29:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/127#issuecomment-439641613  

Resolved ?! - if not open a new PR

---
