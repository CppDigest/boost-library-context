# #119 - Future-proof renames [Closed]

> Username: anarthal  
> Created at: 2023-02-11 12:08:36 UTC  
> Updated at: 2023-02-20 12:54:20 UTC  
> Closed at: 2023-02-20 12:54:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/119  

* resultset => something that describes the results of a query/execution. This may contain more than 1 resultset  
* server_diagnostics => something that is not server-specific. When we develop #60, the client will likely have to generate diagnostics that should go in this structure  
* server_error => rename with the logic above  
* server_category => something that allows introducing MySQL and MariaDB specific categories. Error codes specific to MySQL should be removed from here. See #108.  
* Consider dropping field::emplace_xxxx (I don't think they add any value).
