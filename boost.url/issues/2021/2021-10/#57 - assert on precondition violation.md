# #57 - assert on precondition violation [Closed]

> Username: vinniefalco  
> Created at: 2021-10-09 16:54:41 UTC  
> Updated at: 2021-10-10 04:07:31 UTC  
> Closed at: 2021-10-10 04:07:31 UTC  
> Url: https://github.com/boostorg/url/issues/57  

Functions like `front`, `back` , `operator[]`, and others should use `BOOST_ASSERT` if the precondition is violated.
