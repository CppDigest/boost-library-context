# #612 - overflow reported by oss fuzz 37341 [Closed]

> Username: pauldreik  
> Created at: 2021-08-18 18:08:59 UTC  
> Updated at: 2021-08-20 10:15:25 UTC  
> Closed at: 2021-08-20 10:15:25 UTC  
> Url: https://github.com/boostorg/json/issues/612  

the overflow happens at https://github.com/boostorg/json/blob/3f48a274589aac06280b2c1b0e3d70796cac4b05/include/boost/json/basic_parser_impl.hpp#L2397  
  
the error is   
  
boost/json/basic_parser_impl.hpp:2397:18: runtime error: signed integer overflow: -11 + -2147483640 cannot be represented in type 'int'
