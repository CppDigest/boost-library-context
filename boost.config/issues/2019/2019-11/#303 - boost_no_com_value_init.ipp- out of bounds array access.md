# #303 - boost_no_com_value_init.ipp: out of bounds array access [Open]

> Username: Kojoley  
> Created at: 2019-11-12 23:45:02 UTC  
> Updated at: 2019-11-12 23:45:02 UTC  
> Url: https://github.com/boostorg/config/issues/303  

```  
$ clang++ -I../../.. -fsanitize=undefined config_test.cpp && a  
boost_no_com_value_init.ipp:429:11: runtime error: index 4 out of bounds for type 'char const[4]'  
boost_no_com_value_init.ipp:443:11: runtime error: index 7 out of bounds for type 'char const[7]'  
```
