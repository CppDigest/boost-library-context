# #103 - Invalid date and datetimes [Closed]

> Username: anarthal  
> Created at: 2022-12-05 21:12:39 UTC  
> Updated at: 2022-12-16 11:59:20 UTC  
> Closed at: 2022-12-16 11:59:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/103  

MySQL allows invalid date and datetimes. We currently represent these as NULL and don't allow inserting them into MySQL using statements. Allow reading and writing these values.
