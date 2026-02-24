# #109 - Can't resultset be returned from inside stored-procedure? [Closed]

> Username: lazychase  
> Created at: 2023-01-18 05:45:10 UTC  
> Updated at: 2023-01-18 15:13:09 UTC  
> Closed at: 2023-01-18 15:13:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/109  

This is a function that simply returns 1.  
  
```  
CREATE DEFINER=`root`@`%` PROCEDURE `up_test`()  
LANGUAGE SQL  
NOT DETERMINISTIC  
CONTAINS SQL  
SQL SECURITY DEFINER  
COMMENT ''  
BEGIN  
  
SELECT 1 AS retValue;  
  
END  
```  
  
When I use the stored-procedure returning a value by the select statement, I get the error below.  
  
`sp_badselect [mysql:1312]`  
  
thanks.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-01-18 15:13:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/109#issuecomment-1387232631  

This is a current limitation of the library. Making it work requires support for multi-resultset and OUT parameter binding, as described by #8.  
  
It is something planned for the future (it won't be included in the first release, 1.82). If you can please describe your use case by commenting in #8 that would be awesome, as it could guide the interface and implementation.  
  
Regards,  
Ruben.
