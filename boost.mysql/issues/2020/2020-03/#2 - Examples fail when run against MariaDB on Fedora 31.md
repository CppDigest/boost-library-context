# #2 - Examples fail when run against MariaDB on Fedora 31 [Closed]

> Username: madmongo1  
> Created at: 2020-03-05 13:03:48 UTC  
> Updated at: 2020-03-16 16:59:56 UTC  
> Closed at: 2020-03-16 16:59:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/2  

I looking to test this library, perhaps contribute to it and petition the Boost community for adoption.  
  
I am testing on Fedora 31. This distro substitutes MariaDB for MySQL.  
  
I have encountered two problems:  
  
1. The setup script syntax is not understood by MariaDB. I had to change it to use the older syntax:  
  
```  
-- User  
DROP USER IF EXISTS example_user;  
CREATE USER example_user IDENTIFIED BY 'example_password';  
GRANT ALL PRIVILEGES ON mysql_asio_examples.* TO 'example_user'@`%`;  
FLUSH PRIVILEGES;  
```  
  
2. After this, authentication fails in the example programs:  
  
`Error: Access denied for user 'example_user'@'%' to database 'mysql_native_password': dbaccess_denied_error, error code: mysql:1044`  
  
I am wondering whether the library needs to check database versions during the handshake and act accordingly?  (I am not an expert in the MySQL protocol)

---

## Comment 1

> Username: anarthal  
> Created at: 2020-03-05 21:01:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/2#issuecomment-595444913  

First thanks for your support.  
  
From having a very quick look into MariaDB handshake, it seems that its handshake packet is fully compatible with the MySQL one. It is understanding the authentication plugin (mysql_native_password) as the database, which may mean that we are sending a wrong value in the capability negotiation. I will have a closer look soon and try to add MariaDB to the CI.

---

## Comment 2

> Username: anarthal  
> Created at: 2020-03-16 16:59:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/2#issuecomment-599650785  

- The issue with solving the setup script without an explicit authentication method would break MySQL 8.x CIs, which by default employ the caching_sha2_password auth method, not supported yet. I have split the specification of the auth plugin and the password in two statements, which MariaDB accepts.  
- The handshake failure was caused by a flag missing during the hanshake, which has been deprecated in MySQL but is still required in MariaDB.  
- I have added MariaDB setups in the CI for Windows, Linux and OSX.
