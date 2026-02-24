# #661 - websocket no-alloc guarantee [Open]

> Username: vinniefalco  
> Created at: 2017-07-17 00:16:51 UTC  
> Updated at: 2018-12-21 15:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/661  

Document and ensure that synchronous websocket read and write operations will not allocate memory when the permessage-deflate extension is not negotiated on an active connection.
