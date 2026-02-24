# #367 - Remove UB regarding connection state for async functions in any_connections [Open]

> Username: anarthal  
> Created at: 2024-10-05 12:43:56 UTC  
> Updated at: 2024-10-05 12:43:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/367  

We currently have a surprisingly big number of places where initiating an async operation derives in unpredictable and/or undefined behavior, . Since the introduction of any_connection, which does not expose the stream, all of these can be fixed to fail in a predictable way, without overhead. These are:  
  
- Initiating any async op while another op is outstanding.  
- Initiating any op after a previous op failed with a fatal error.  
- Initiating any op after a multi-function operation is initiated but not logically completed.  
- We can probably improve diagnostics when trying to use an unconnected connection.  
  
A simple state machine in any_connection should suffice.
