# #1 - Include path conflicts with c mysql installation [Closed]

> Username: madmongo1  
> Created at: 2020-03-05 12:03:02 UTC  
> Updated at: 2020-03-21 20:48:54 UTC  
> Closed at: 2020-03-21 20:48:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/1  

Eventually, you will want an `install` target for this library.  
  
My concern is that the include path `include/mysql` is already occupied by the offical `mysql-c-connector` and mariadb packages on linux systems. This will cause an include path conflict if this library ends up being installed on a linux system (likely!)  
  
My suggestion would be to either name the include directory something like `mysql-asio` or in anticipation of pushing for boost acceptance, `boost/mysql` or `boost/mysql-asio`.
