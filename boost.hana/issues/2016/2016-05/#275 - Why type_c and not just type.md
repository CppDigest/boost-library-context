# #275 - Why type_c and not just type [Closed]

> Username: viboes  
> Created at: 2016-05-21 08:10:04 UTC  
> Updated at: 2016-05-24 20:30:32 UTC  
> Closed at: 2016-05-22 09:33:26 UTC  
> Url: https://github.com/boostorg/hana/issues/275  

What was the rationale to name the type wrapper function `type_c` and not just `type`?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-05-21 17:06:40 UTC  
> Updated at: 2016-05-21 17:12:09 UTC  
> Url: https://github.com/boostorg/hana/issues/275#issuecomment-220789156  

`hana::type_c` is a constexpr variable template. I believe the suffix '_c' is used to denote a constexpr value as you will see with others like `hana::int_c`, `hana::bool_c` and even the user defined literals like '5_c'.  
  
It may also be worth noting that an expression like `hana::type_c<void>` is actually a const lvalue because it is the name of a variable [template].

---

## Comment 2

> Username: viboes  
> Created at: 2016-05-22 09:33:26 UTC  
> Url: https://github.com/boostorg/hana/issues/275#issuecomment-220822940  

Thanks for the explanation.
