# #215 - SQL formatting - injection fuzzing [Closed]

> Username: anarthal  
> Created at: 2024-02-13 11:08:16 UTC  
> Updated at: 2024-03-16 10:09:40 UTC  
> Closed at: 2024-03-16 10:09:40 UTC  
> Url: https://github.com/boostorg/mysql/issues/215  

SQL formatting is security critical. We should ensure no value results in SQL injection.  
  
This fuzzing tests should ensure that SQL injection is not possible when formatting individual values (specially strings). These should focus on testing the validity of the generated queries (probably with a real MySQL system), rather than on not invoking C++ UB (see #214 for this).  
  
Some material:  
*https://security.stackexchange.com/questions/9908/multibyte-character-exploits-php-mysql  
* https://sqlmap.org/  
* https://github.com/andygrove/sqlfuzz
