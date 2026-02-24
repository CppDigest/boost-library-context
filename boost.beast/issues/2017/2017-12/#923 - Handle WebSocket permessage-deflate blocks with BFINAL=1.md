# #923 - Handle WebSocket permessage-deflate blocks with BFINAL=1 [Open]

> Username: vinniefalco  
> Created at: 2017-12-04 19:21:57 UTC  
> Updated at: 2017-12-04 19:21:57 UTC  
> Url: https://github.com/boostorg/beast/issues/923  

The permessage-deflate extension specification allows deflate blocks with the BFINAL set, but Beast handles them by generating an error. This affects few to no users in practice, since no existing implementation of the permessage-deflate extension generates intermediate deflate blocks with the BFINAL bit set (they can only be generated manually such as in a unit test or fuzzer). Still, the implementation should handle them correctly.
