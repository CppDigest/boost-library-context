# #8 - No compile-time error when incorrectly using BOOST_DESCRIBE_STRUCT on an enum type [Closed]

> Username: ddevienne  
> Created at: 2021-03-08 12:27:35 UTC  
> Updated at: 2021-03-18 00:51:54 UTC  
> Closed at: 2021-03-18 00:51:54 UTC  
> Url: https://github.com/boostorg/describe/issues/8  

Made that mistake, because my _descriptions_ are not hand-written, but code-generated,  
and I forgot that container I was using had all types, not just structs. I fixed my generator  
since, but the code should probably `static_assert` the type is not an enum (or primitive).  
  
Of course, I generated something like  
```  
BOOST_DESCRIBE_STRUCT(  
    MyEnum, (),  
    () // No state  
)  
```
