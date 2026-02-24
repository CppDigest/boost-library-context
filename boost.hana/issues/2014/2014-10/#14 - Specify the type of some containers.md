# #14 - Specify the type of some containers [Closed]

> Username: ldionne  
> Created at: 2014-10-15 14:14:03 UTC  
> Updated at: 2015-05-28 23:28:32 UTC  
> Closed at: 2015-05-28 23:28:32 UTC  
> Url: https://github.com/boostorg/hana/issues/14  

Right now, the actual C++ type of Hana's heterogeneous containers is never specified. For example, `tuple(1, '2')` is an object whose type is unspecified (and in fact useless because it involves the type of a lambda and other stuff). This is mainly to give room to the implementation. However, it would definitely add value if the type of containers was specified (or at least more specified), so we could pattern match on them and do other useful stuff:  
  
``` cpp  
template <typename ...T>  
void f(hana::tuple_type<T...> t);  
```  
  
If this is feasible, i.e. if the library can still be implemented while specifying the type of containers, a possible way to achieve this would be:  
- `hana::tuple<...>` is a type (like `std::tuple<...>`), and `hana::make<hana::Tuple>` is equivalent to the current `hana::tuple` function  
- `hana::map<...>` is a type, and `hana::make<hana::Map>` creates one of these.  
- same for `hana::pair<>`, `hana::set<>` & al.  
## Things to consider if we go this way  
- The compile-time performance of containers could be influenced by longer names to mangle. We'd have to benchmark the difference.  
- Some objects just can't have their type fully specified. For example, `hana::type<T>` has to hide `T` from ADL, which requires it being a dependent type. In this precise case, what we could specify is that `type<T>` is of a type which derives from something that's specified. That would still allow us to pattern match in function templates and perhaps other contexts. In general, we would need to document exactly what is defined and what is left undefined about the type of each object.  
- We might end up with naming conflicts, e.g. having a function which should really have the same name as the type of some object. Before we do what's being proposed here, we should have a clear plan of how we're going to rename things so everything stays consistent.

---

## Comment 1

> Username: brunocodutra  
> Created at: 2015-03-03 03:40:53 UTC  
> Url: https://github.com/boostorg/hana/issues/14#issuecomment-76881620  

IMO this is a very important feature for functional programming.   
  
I was thinking for instance about how to port [metalog](https://github.com/brunocodutra/metalog) from MPL to Hana and my first concern was precisely that it couldn't rely on function overloading analogously to how it currently relies on template overloading. Take for example the simple snippet bellow.  
  
``` c++  
/** WARNING - untested code follows **/  
  
template <typename HL, typename ...TL, typename HR, typename ...TR>  
bool unify(hana::tuple_type<HL, TL...> lhs, hana::tuple_type<HR, TR...> rhs) {  
    return unify(hana::head(lhs), hana::head(rhs)) ?   
        unify(hana::tail(lhs), hana::tail(rhs)) : false;   
}  
template <typename HL, typename HR>  
bool unify(hana::tuple_type<HL> lhs, hana::tuple_type<HR> rhs) {  
    return unify(hana::head(lhs), hana::head(rhs));   
}  
template <typename T>  
bool unify(T lhs, T rhs) {  
    return lhs == rhs;   
}  
[...]  
```  
  
I don't really know how one could write it as expressively without being able to overload functions. This is, by the way, the reason why metalog currently doesn't support MPL's sequences as instances of _unifiable expressions_.
