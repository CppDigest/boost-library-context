# #179 - Reduce network_algorithms code duplication via sans-io [Closed]

> Username: anarthal  
> Created at: 2023-10-16 09:58:40 UTC  
> Updated at: 2023-12-26 16:04:11 UTC  
> Closed at: 2023-12-26 16:04:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/179  

We currently have a lot of duplication in `network_algorithms` (async vs. sync), and the structure makes testing very difficult. This can possibly be simplified using sans-io algorithms, separating concerns.
