# #238 - CMake: unify workflow [Closed]

> Username: anarthal  
> Created at: 2024-03-16 10:44:32 UTC  
> Updated at: 2024-03-18 16:46:34 UTC  
> Closed at: 2024-03-18 16:46:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/238  

We currently support two CMake workflows:  
1. When we're the project root, we `find_package` Boost.  
2. When we're running as part of the superproject, we add dependencies directly.  
  
We only use 1. to develop locally. But it's cumbersome when we need to test changes applied to Boost dependencies - we need to create a Boost installation locally and point the project to it. Even then, it sometimes creates problems if we don't `rm -rf` the installed Boost.MySQL before using the project. CIs perform duplicate work for these workflows.  
  
I'd like to get rid of workflow 1. in favor of 2.
