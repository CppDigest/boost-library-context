# #154 - Blob types in stored procedures get the wrong column_type under MariaDB [Closed]

> Username: anarthal  
> Created at: 2023-05-22 12:01:17 UTC  
> Updated at: 2023-05-24 10:19:13 UTC  
> Closed at: 2023-05-24 10:19:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/154  

MariaDB sometimes sends `protocol_field_type::medium_blob` and friends instead of plain `protocol_field_type::blob`.
