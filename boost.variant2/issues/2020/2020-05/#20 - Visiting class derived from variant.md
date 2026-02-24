# #20 - Visiting class derived from variant [Closed]

> Username: sdebionne  
> Created at: 2020-05-06 07:43:34 UTC  
> Updated at: 2020-05-07 16:12:54 UTC  
> Closed at: 2020-05-07 16:12:53 UTC  
> Url: https://github.com/boostorg/variant2/issues/20  

Given  
  
```c++  
template <typename ...Types>  
struct my_variant : boost::variant2::variant<Types...> { };  
```  
  
I looks like it is not possible to visit `my_variant`:  
  
```c++  
boost::variant2::variant<int, double> v1;  
my_variant<int, double> v2;  
  
auto visitor = [](auto&& v) { std::cout << v << std::endl; };  
  
variant::visit(visitor, v1); //OK  
variant::visit(visitor, v2); //KO error: 'value' is not a member of 'boost::variant2::variant_size<my_variant<...>>  
```  
  
This kind of construct is used in Boost.GIL for example and we would like to deprecate `gil::apply_operations` in the future.

---

## Comment 1

> Username: sdebionne  
> Created at: 2020-05-06 07:59:48 UTC  
> Url: https://github.com/boostorg/variant2/issues/20#issuecomment-624500212  

As a side note, this works with Boost.Variant:  
  
```c++  
template <typename... Types>  
struct my_legacy_variant : boost::variant<Types...> {};  
```  
  
```c++  
my_legacy_variant<int, double> v3;  
...  
boost::apply_visitor(visitor, v3);  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2020-05-06 13:53:55 UTC  
> Url: https://github.com/boostorg/variant2/issues/20#issuecomment-624663470  

Sounds like the specification of std::variant is being fixed to support the derivation case, so I suppose variant2 should too.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-05-06 19:33:35 UTC  
> Url: https://github.com/boostorg/variant2/issues/20#issuecomment-624846473  

Should work with the current `develop`.

---

## Comment 4

> Username: sdebionne  
> Created at: 2020-05-07 06:36:45 UTC  
> Url: https://github.com/boostorg/variant2/issues/20#issuecomment-625059842  

Can I assume this will be released in 1.74?

---

## Comment 5

> Username: pdimov  
> Created at: 2020-05-07 14:13:45 UTC  
> Url: https://github.com/boostorg/variant2/issues/20#issuecomment-625281129  

Sure, unless we find something very wrong with it, but that's not likely.

---

## Comment 6

> Username: sdebionne  
> Created at: 2020-05-07 16:12:53 UTC  
> Url: https://github.com/boostorg/variant2/issues/20#issuecomment-625351571  

I try 772ef0d312868a1bdb371e8f336d5abd41cc61b2 with our codebase, so far so good, thanks!
