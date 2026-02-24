# #59 - Return resultset by lvalue reference [Closed]

> Username: anarthal  
> Created at: 2022-05-11 09:46:42 UTC  
> Updated at: 2023-01-14 23:06:43 UTC  
> Closed at: 2023-01-14 23:06:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/59  

The current interface returns `resultset` objects by value (for sync operations) and as completion handler arguments (for async operations). Returning resultsets by lvalue reference, instead, increases the chances of memory re-using.
