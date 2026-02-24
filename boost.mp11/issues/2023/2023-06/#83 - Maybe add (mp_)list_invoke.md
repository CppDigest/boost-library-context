# #83 - Maybe add (mp_)list_invoke [Open]

> Username: pdimov  
> Created at: 2023-06-23 16:25:22 UTC  
> Updated at: 2023-06-23 16:25:22 UTC  
> Url: https://github.com/boostorg/mp11/issues/83  

```  
template<class L, class F> auto list_invoke( F&& f );  
  
// Returns: f( T{}... ), where T... are the elements of L, or mp_value<A>{}..., for a value list  
```  
  
Motivation: https://twitter.com/ericniebler/status/1671986987396415491  
  
`list_invoke<mp_iota_c<5>>( [&](auto... I){ ... } );`  
  
Name TBD. mp_with_list?
