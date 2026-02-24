# #549 - Adding value_from support without including a Boost.JSON header [Closed]

> Username: pdimov  
> Created at: 2021-04-27 14:33:47 UTC  
> Updated at: 2021-10-20 19:35:59 UTC  
> Closed at: 2021-10-20 19:35:59 UTC  
> Url: https://github.com/boostorg/json/issues/549  

It's important for class authors to be able to write the appropriate `tag_invoke` overload for their types without needing to include a Boost.JSON header (in order to not require Boost.JSON to be present when not used.) A minimal example is https://godbolt.org/z/jqMj4o4j4. The immediate problem is that `value_from` can't be forward declared because it has a default argument.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-04-27 14:51:04 UTC  
> Url: https://github.com/boostorg/json/issues/549#issuecomment-827667652  

These declarations should have comments explaining the rationale for their signatures

---

## Comment 2

> Username: pdimov  
> Created at: 2021-04-27 16:14:13 UTC  
> Url: https://github.com/boostorg/json/issues/549#issuecomment-827733301  

The easiest fix here is to change  
```  
template<class T> value value_from( T&& t, storage_ptr sp = {} );  
```  
to  
```  
template<class T> value value_from( T&& t );  
template<class T> value value_from( T&& t, storage_ptr sp );  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2021-04-27 16:24:33 UTC  
> Url: https://github.com/boostorg/json/issues/549#issuecomment-827740390  

... however, doing so still doesn't enable the code to compile, because `value_from` returns `value` by value, and `value` is incomplete, so the API as-is can't be used with only forward declarations.  
  
So instead of changing the above declaration, we need to introduce another API that works when `value` is incomplete:  
```  
template<class T> void value_from_( value& v, T&& t );  
```  
with some better name.  
  
The `tag_invoke` overload then becomes  
```  
template<class W> void tag_invoke( boost::json::value_from_tag const&, W& w, X const& x )  
{  
    auto& obj = w.emplace_object();  
    value_from_( obj[ "m" ], x.m );  
}  
```  
which is even easier to use and automatically uses the right storage. (And the signature matches that of `tag_invoke`.)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-04-27 17:14:56 UTC  
> Url: https://github.com/boostorg/json/issues/549#issuecomment-827772699  

> we need to introduce another API   
  
Is this a public API?

---

## Comment 5

> Username: pdimov  
> Created at: 2021-04-27 17:15:44 UTC  
> Url: https://github.com/boostorg/json/issues/549#issuecomment-827773258  

How would anyone use it otherwise?
