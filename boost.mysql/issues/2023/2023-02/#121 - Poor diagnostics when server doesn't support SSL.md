# #121 - Poor diagnostics when server doesn't support SSL [Closed]

> Username: anarthal  
> Created at: 2023-02-20 01:20:30 UTC  
> Updated at: 2023-02-20 12:54:23 UTC  
> Closed at: 2023-02-20 12:54:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/121  

When the server does not support SSL, we currently return a generic unsupported_server error with an ambiguous description, which ends up in users opening issues against the repo. Unsupported SSL can mean that the server doesn't have the adequate cryptographic material configured, so it's pretty common. Add a separate code providing appropriate diagnostics for this case.
