# #190 - Sync functions returning values trigger assertion when failing [Closed]

> Username: anarthal  
> Created at: 2023-12-19 15:12:20 UTC  
> Updated at: 2023-12-26 16:04:11 UTC  
> Closed at: 2023-12-26 16:04:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/190  

(Only affecting unreleased code - 1.84 is NOT affected)  
  
Sync functions returning values (`prepare_statement` and `read_some_rows`) trigger an assertion when they fail. Find out why unit tests did not find the issue.
