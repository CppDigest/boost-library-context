# #104 - Allow using MYSQL_TYPE_BLOB in statement parameters [Closed]

> Username: anarthal  
> Created at: 2022-12-05 21:39:14 UTC  
> Updated at: 2022-12-16 11:59:20 UTC  
> Closed at: 2022-12-16 11:59:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/104  

Currently, all strings are sent with MYSQL_TYPE_STRING type when used as statement parameters. Although that produces correct results almost everywhere, MySQL recommends using MYSQL_TYPE_BLOB for binary strings. This also allows to bypass character set conversion when inserting strings.  
  
Distinguish between binary and character strings in client code.
