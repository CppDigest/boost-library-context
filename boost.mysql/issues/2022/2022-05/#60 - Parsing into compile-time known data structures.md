# #60 - Parsing into compile-time known data structures [Closed]

> Username: anarthal  
> Created at: 2022-05-11 10:30:36 UTC  
> Updated at: 2023-05-24 10:19:13 UTC  
> Closed at: 2023-05-24 10:19:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/60  

Expose a native API that allows users to parse rows directly into custom, compile-time data structures. E.g.  
  
```  
std::tuple<T1, T2, T3> tuple_row;  
resultset.read_one(tuple_row)  
```  
  
```  
struct X  
{  
    T1 m1;  
    T2 m2;  
};  
  
BOOST_DESCRIBE_STRUCT(X, (), (m1, m2))  
  
X struct_row;  
resultset.read_one(struct_row)  
  
```
