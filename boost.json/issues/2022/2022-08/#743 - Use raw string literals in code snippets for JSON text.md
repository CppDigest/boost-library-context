# #743 - Use raw string literals in code snippets for JSON text [Closed]

> Username: grisumbras  
> Created at: 2022-08-28 03:45:34 UTC  
> Updated at: 2023-04-02 18:44:45 UTC  
> Closed at: 2023-04-02 18:44:45 UTC  
> Url: https://github.com/boostorg/json/issues/743  

Examples of input JSON-s contain a lot of escape characters in string  
literals, like in https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/dom/conversion.html  
This makes it difficult to read. Consider using raw string literals.  
Instead of  
```c++  
assert( serialize( jv ) == "{\"x\":4,\"y\":1,\"z\":4}" );  
```  
We would have  
```c++  
assert( serialize( jv ) == R"({"x":4,"y":1,"z":4})" );  
```
