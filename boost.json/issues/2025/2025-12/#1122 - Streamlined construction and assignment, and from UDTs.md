# #1122 - Streamlined construction and assignment, and from UDTs [Closed]

> Username: vinniefalco  
> Created at: 2025-12-01 16:22:01 UTC  
> Updated at: 2025-12-03 21:46:33 UTC  
> Closed at: 2025-12-03 21:46:33 UTC  
> Url: https://github.com/boostorg/json/issues/1122  

I think we should explore getting rid of most of the constructors and assignment operators in json::value and replace them with this  
```  
template< class T >  
value( T&& t );  
  
template< class T >  
value& operator=( T&& t );  
```  
  
and in the same exploration, allow `value_ref` to type erase UDTs. This will allow `jv( t.a )` and `jv = t.a` and `jv = vec` to work.  
  
The way to do this is to add another constrained constructor to `value_ref` which accepts UDTs when a suitable `tag_invoke` exists, and then type erase it by storing a function pointer which, when invoked, calls `tag_invoke` with the storage_ptr and produces the `json::value`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-12-01 16:23:05 UTC  
> Url: https://github.com/boostorg/json/issues/1122#issuecomment-3597490933  

@claude
