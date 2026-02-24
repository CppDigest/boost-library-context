# #120 - Docs for tuple-like interface [Closed]

> Username: vinniefalco  
> Created at: 2020-06-30 20:14:58 UTC  
> Updated at: 2020-08-12 00:25:40 UTC  
> Closed at: 2020-08-12 00:25:40 UTC  
> Url: https://github.com/boostorg/json/issues/120  

`get` will be missing some javadocs when the PR is merged. It should probably be done like this:  
```  
#ifdef BOOST_JSON_DOCS  
  
/** Tuple-like interface  
*/  
template< std::size_t I, class T >  
__see_below__  
get( T&& );  
  
#else  
  
// actual defintions  
  
#endif  
```

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-06-30 20:20:50 UTC  
> Url: https://github.com/boostorg/json/issues/120#issuecomment-652021783  

Exposition could use some work:  
  
> As with `std::pair`, `key_value_pair` implements the required specializations of `std::tuple_element`, `std::tuple_size`, and get to function as a tuple-like type. This means that it can be used as the initializer in a structured binding declaration: [snippet]
