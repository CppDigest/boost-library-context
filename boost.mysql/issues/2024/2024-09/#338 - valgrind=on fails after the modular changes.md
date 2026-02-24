# #338 - valgrind=on fails after the modular changes [Closed]

> Username: anarthal  
> Created at: 2024-09-01 11:44:21 UTC  
> Updated at: 2024-09-05 15:00:39 UTC  
> Closed at: 2024-09-05 15:00:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/338  

We're currently taking advantage of the `valgrind` b2 feature that was present in another Boost library before the modular refactor. This used to work, but doesn't anymore. The right thing is to define our own feature, as Beast does.  
  
CIs are currently failing because of this.
