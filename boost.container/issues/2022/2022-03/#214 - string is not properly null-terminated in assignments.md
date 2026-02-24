# #214 - string is not properly null-terminated in assignments [Closed]

> Username: igaztanaga  
> Created at: 2022-03-06 21:36:04 UTC  
> Updated at: 2022-03-06 21:38:08 UTC  
> Closed at: 2022-03-06 21:38:08 UTC  
> Url: https://github.com/boostorg/container/issues/214  

According to the test report in https://github.com/boostorg/interprocess/issues/167, string container is not properly null terminated when we already have enougth storage. We should add a priv_construct_null call at the end of the operation
