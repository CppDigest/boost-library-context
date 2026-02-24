# #93 - Static functions to create error-holding result and value-holding result [Closed]

> Username: grisumbras  
> Created at: 2022-09-26 09:42:38 UTC  
> Updated at: 2022-09-27 17:32:40 UTC  
> Closed at: 2022-09-27 17:32:40 UTC  
> Url: https://github.com/boostorg/system/issues/93  

The constructors that use special tag types from namespace `boost::system` has the unfortunate effect that one can't write a function template that (reliably) creates a result object without including some Boost.System headers. Thus it creates a physical dependency on System. For example, see this: https://godbolt.org/z/7KsKfreKe.  
  
This can be solved with a pair of static functions `result::make_error` and `result::make_value`.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-09-26 09:45:27 UTC  
> Url: https://github.com/boostorg/system/issues/93#issuecomment-1257774792  

BTW, while we are there, having nested type aliases for value type and error type can also be useful.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-09-27 17:13:17 UTC  
> Url: https://github.com/boostorg/system/issues/93#issuecomment-1259809105  

It turned out that static functions don't work when the value type is noncopyable, so I've added `in_place_value` and `in_place_error` static members to `result` instead. This allows `R( R::in_place_value, args... )` and `R( R::in_place_error, args... )`.
