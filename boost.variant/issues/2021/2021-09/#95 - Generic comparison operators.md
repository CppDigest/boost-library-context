# #95 - Generic comparison operators [Open]

> Username: apolukhin  
> Created at: 2021-09-20 17:34:12 UTC  
> Updated at: 2021-09-20 17:34:12 UTC  
> Url: https://github.com/boostorg/variant/issues/95  

It could be useful to have comparison operators with types that are held in variant. For example:   
```  
variant<int, string> v1(1), v2("2");  
if (v1 < 1 || v2 > "hello") {  
    // ...  
}  
  
if (v1 < 1.0) { // compilation error  
    // ...  
}  
```  
  
Issue was imported from [Trac 10845](https://svn.boost.org/trac10/ticket/10845)
