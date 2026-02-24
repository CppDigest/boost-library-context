# #233 - Update workflow: always use PRs [Closed]

> Username: anarthal  
> Created at: 2024-03-14 14:34:47 UTC  
> Updated at: 2024-03-14 16:18:06 UTC  
> Closed at: 2024-03-14 16:18:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/233  

We currently use plain branches with merge squash for simple features and PRs for complex ones. PRs trigger duplicate CI workflows currently, which is a pain. Unify these workflows to use PRs.
