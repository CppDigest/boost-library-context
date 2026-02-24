# #115 - Unknown default auth plugin [Open]

> Username: anarthal  
> Created at: 2023-02-06 11:00:19 UTC  
> Updated at: 2023-02-06 11:00:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/115  

Currently, when the server has a default auth plugin, we fail handshake(). That's not what the official libraries do. In case that the default auth plugin is unknown but the user employs a known auth plugin, we should succeed handshake().
