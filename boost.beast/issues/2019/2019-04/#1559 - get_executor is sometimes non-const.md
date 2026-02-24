# #1559 - get_executor is sometimes non-const [Closed]

> Username: vinniefalco  
> Created at: 2019-04-06 16:59:12 UTC  
> Updated at: 2019-04-20 19:05:08 UTC  
> Closed at: 2019-04-20 19:05:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1559  

declarations of `get_executor` should be `const` when `*this` is a completion handler, otherwise should be non-const. And always `noexcept`.
