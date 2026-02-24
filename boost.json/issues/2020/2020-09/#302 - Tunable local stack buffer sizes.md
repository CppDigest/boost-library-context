# #302 - Tunable local stack buffer sizes [Closed]

> Username: vinniefalco  
> Created at: 2020-09-08 14:45:32 UTC  
> Updated at: 2020-09-08 15:59:20 UTC  
> Closed at: 2020-09-08 15:59:20 UTC  
> Url: https://github.com/boostorg/json/issues/302  

A macro `BOOST_JSON_STACK_BUFFER_SIZE`, user-definable, and automatically set to 4096 if Intel is detected, else 256. This will be the size for all local buffers.
