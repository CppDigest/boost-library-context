# #80 CI & GCC 4.4.7 [Merged]

> Username: RobertLeahy  
> Created at: 2019-12-23 17:03:05 UTC  
> Updated at: 2020-04-05 22:43:23 UTC  
> Merged at: 2020-04-05 22:43:23 UTC  
> Closed at: 2020-04-05 22:43:23 UTC  
> Url: https://github.com/boostorg/optional/pull/80  

- Fixed an issue with `.travis.yml` which was causing CI to fail for Clang 5  
- Unit tests now build & pass under GCC 4.4.7  
- Fixed a compile issue encountered under GCC 4.4.7 when assigning to a `boost::optional<T>` where `T` was trivially copyable

---
