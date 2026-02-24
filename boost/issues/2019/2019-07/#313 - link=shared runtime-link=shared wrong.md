# #313 - link=shared runtime-link=shared wrong [Closed]

> Username: polelf  
> Created at: 2019-07-27 05:03:40 UTC  
> Updated at: 2019-07-27 05:57:17 UTC  
> Closed at: 2019-07-27 05:57:17 UTC  
> Url: https://github.com/boostorg/boost/issues/313  

b2.exe --with-date_time address-model=32 threading=multi link=shared runtime-link=shared release  
b2.exe --with-date_time address-model=32 threading=multi link=shared runtime-link=static release  
  
just got one dll boost_date_time-vc142-mt-x32-1_70.dll  
why?
