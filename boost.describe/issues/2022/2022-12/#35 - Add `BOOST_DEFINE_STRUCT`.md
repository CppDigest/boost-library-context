# #35 - Add `BOOST_DEFINE_STRUCT` [Open]

> Username: denzor200  
> Created at: 2022-12-12 15:31:58 UTC  
> Updated at: 2022-12-12 15:31:58 UTC  
> Url: https://github.com/boostorg/describe/issues/35  

Like this:  
```  
BOOST_DEFINE_STRUCT(X, (), (  
    (int, m1),  
    (int, m2)  
));  
```  
Gives the similar result as here: https://www.boost.org/doc/libs/master/libs/describe/doc/html/describe.html#classes_class_types_with_public_members
