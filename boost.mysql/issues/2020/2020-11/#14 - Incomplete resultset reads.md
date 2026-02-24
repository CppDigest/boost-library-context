# #14 - Incomplete resultset reads [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:29:27 UTC  
> Updated at: 2022-12-31 14:48:28 UTC  
> Closed at: 2022-12-31 14:48:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/14  

Currently, starting a request to the server (e.g. query, prepare_statement, prepared_statement::execute) before fully reading a resultset results in undefined behavior. Check if there is any way of lifting this restriction (e.g. using packet sequence number) or provide a way to completely read and discard all remaining packets in a resultset.

---

## Comment 1

> Username: anarthal  
> Created at: 2022-12-31 14:48:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/14#issuecomment-1368225116  

Not as useful with the new single-function + multi-function approach.
