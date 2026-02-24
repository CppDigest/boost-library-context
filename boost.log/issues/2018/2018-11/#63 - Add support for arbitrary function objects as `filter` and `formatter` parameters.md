# #63 - Add support for arbitrary function objects as `filter` and `formatter` parameters [Closed]

> Username: Lastique  
> Created at: 2018-11-26 23:51:41 UTC  
> Updated at: 2019-06-24 01:06:40 UTC  
> Closed at: 2019-06-24 01:06:40 UTC  
> Url: https://github.com/boostorg/log/issues/63  

This would be especially useful for C++11 lambdas so that code like this works:  
  
```  
logging::add_file_log(  
    keywords::file_name = "A.log",  
    keywords::filter = [](boost::log::attribute_value_set const& attrs) { return true; }  
);  
```
