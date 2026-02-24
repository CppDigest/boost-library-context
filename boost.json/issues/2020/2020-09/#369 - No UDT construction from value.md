# #369 - No UDT construction from value [Closed]

> Username: vinniefalco  
> Created at: 2020-09-19 18:50:25 UTC  
> Updated at: 2022-08-01 05:18:37 UTC  
> Closed at: 2022-08-01 05:18:37 UTC  
> Url: https://github.com/boostorg/json/issues/369  

In Value Conversion, Converting to Foreign Types, it's shown that defining a constructor  
```  
    customer::customer( value const& jv );  
```  
  
enables `value_to<customer>(jv)` to work. This is, in my opinion, a legacy mechanism made obsolete by `tag_invoke` and should not be supported. This example should be removed from the documentation, and only the `tag_invoke` way should be featured.
