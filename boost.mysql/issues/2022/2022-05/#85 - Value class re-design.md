# #85 - Value class re-design [Closed]

> Username: anarthal  
> Created at: 2022-05-15 13:44:48 UTC  
> Updated at: 2022-12-16 11:59:22 UTC  
> Closed at: 2022-12-16 11:59:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/85  

The value class currently doesn't own the memory it points to, in the case of string values. This causes confusion and is error prone, and may not work well with some better buffering strategies. Make the value class own its memory.  
  
As part of this redesign, we should also change how `value::get`, `value::get_optional` and `value::get_std_optional` work. With owning values, calling this functions would end up copying the string, which is undesirable. We may get rid of conversions at all and provide something similar to `variant2::get_if`.  
  
Exposing the underlying variant may also be problematic. Consider removing this function.  
  
Handling `NULL` values and type mismatches the same in `get_optional` also causes confusion. A user proposed enabling `get<optional<T>>()` as a way to specify "I'm trying to access a value of type `T` which may be `NULL`.  
  
Users find the behavior for relational operators, specially operator< and friends, confusing.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-05-15 16:46:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/85#issuecomment-1126976807  

I don't like exposing the variant, so +1 to removing it from the public interface. In fact I would just use a `union` and manually manage it, because variant2 has a lot of header material to handle noexcept propagation and all that metaprogramming which just isn't needed here.  
  
There is nothing wrong in principle with `value` having reference semantics and not owning the storage for strings. However, the user needs to be able to construct something which does have ownership. Given:  
  
```  
value v = f(...); // non-owning strings  
```  
  
A user should be able to take ownership by constructing a new type provided by the library, for example:  
  
```  
owning_value vv( v ); // owning strings  
```  
  
Where `owning_value` is a library-provided type. The idiom for this is to use the container's nested `reference` and `value_type` types.

---

## Comment 2

> Username: anarthal  
> Created at: 2022-05-18 23:06:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/85#issuecomment-1130694845  

Users have mentioned that having it called `value` and be a view may be confusing. If we keep it a view, consider naming it `sql_value_view` or `field_view`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-19 01:34:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/85#issuecomment-1130952033  

Yes that's good advice. `field` and `field_view`?

---

## Comment 4

> Username: anarthal  
> Created at: 2022-05-19 10:11:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/85#issuecomment-1131503516  

Should work
