# #73 - Prepared statement and resultset types are verbose [Closed]

> Username: anarthal  
> Created at: 2022-05-12 18:10:15 UTC  
> Updated at: 2023-01-14 23:06:42 UTC  
> Closed at: 2023-01-14 23:06:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/73  

As they depend on the Stream type, they get quite verbose, especially for SSL. Suggestions:  
  
* Add type aliases `connection::prepared_statement_type` and `connection::resultset_type`.  
* Make SSL types the default (i.e. `tcp_connection` would use SSL), and rename the plain text one to `tcp_plaintext_connection`.  
* Make `resultset` and `prepared_statement` not be I/O objects.  
* Put the type aliases under namespaces.
