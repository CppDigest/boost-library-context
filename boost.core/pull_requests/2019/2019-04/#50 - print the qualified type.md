# #50 print the qualified type [Closed]

> Username: HDembinski  
> Created at: 2019-04-18 14:08:37 UTC  
> Updated at: 2019-04-24 10:45:10 UTC  
> Closed at: 2019-04-20 14:02:22 UTC  
> Url: https://github.com/boostorg/core/pull/50  

`BOOST_TEST_TRAIT_SAME(T, T const)` produced the right result, but the wrong error message. It used to say  
```  
'int main()' ('T' != 'T')  
```  
This patch prints the correctly qualified type name  
```  
'int main()' ('T' != 'T const')  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-04-18 14:42:09 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484539294  

`stream` should be `print`; `T*` seems unnecessary, `T&&` is missing.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-04-19 08:17:22 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484804364  

A question: should this functionality go to core/typeinfo.hpp? It would be handy, for one thing, and even more, this line seems to compare the type names without qualifiers on some platforms, which would give wrong results: https://github.com/boostorg/core/blob/87026641885fdcea0d34439c10a9d80296664429/include/boost/core/typeinfo.hpp#L52  
  
Putting the correct type name into core::typeinfo should fix this. I understand that core::typeinfo is supposed to be a drop-in replacement for std::typeinfo, so `core::typeinfo::name()` probably should still return the type name without qualifiers, but perhaps we could add an new method `core::typeinfo::full_name()` or something, which returns the correct type name.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2019-04-19 09:01:28 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484818059  

Looks like we are allowed to return a qualified name with the `name()` method, since there is basically no guarantee given regarding the name.  
https://en.cppreference.com/w/cpp/types/type_info/name

---

## Comment 4

> Username: HDembinski  
> Created_at: 2019-04-19 10:42:06 UTC  
> Updated_at: 2019-04-19 10:42:47 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484847681  

Thinking more about it, maybe this is the wrong approach to solve this issue. I am seeing this problem only because the `std::typeinfo::name()` method on my system doesn't show the qualifiers, but on another system it might. In this case, the current solution may return `T&&` for a `T&`, which would be another bug.  
  
A better and less platform-dependent solution would be the following:  
```  
template <class T> struct quote {};  
#define BOOST_CORE_TYPEID(T) typeid(quote<T>)  
```  
Then when the name is returned, skip all characters up to the "quote<" string and remove the last ">". This can be done also for std::typeinfo, by inheriting from it.

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-04-19 11:49:33 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484869604  

`typeid(T)` strips references and cv qualifiers. See http://eel.is/c++draft/expr.typeid#4.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-04-19 11:51:49 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484870107  

The `boost/config.hpp` include should come after the `core` includes.

---

## Comment 7

> Username: HDembinski  
> Created_at: 2019-04-19 14:24:29 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484911330  

Why is it important to have the config header after the core headers?

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-04-19 14:38:29 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484915758  

It masks errors stemming from one of the headers using Config without including it.

---

## Comment 9

> Username: HDembinski  
> Created_at: 2019-04-19 15:27:44 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-484931167  

Ok, make sense.

---

## Comment 10

> Username: glenfe  
> Created_at: 2019-04-20 03:47:13 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-485055765  

I don't like that this uses `BOOST_LIGHTWEIGHT_TEST_OSTREAM` more than once for the same error expression.  
  
In the expression, each  `boost::core::demangled_name(BOOST_CORE_TYPEID(X))` can just become `our_type<X>()` and the implementation becomes cleaner.  
  
This also does not handle `const volatile`.   
  
See #54.

---

## Comment 11

> Username: HDembinski  
> Created_at: 2019-04-20 11:49:12 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-485101688  

I agree that @glenfe s version looks nicer.

---

## Comment 12

> Username: pdimov  
> Created_at: 2019-04-20 14:02:22 UTC  
> Url: https://github.com/boostorg/core/pull/50#issuecomment-485125575  

Closing in favor of #54. Thanks Hans.

---
