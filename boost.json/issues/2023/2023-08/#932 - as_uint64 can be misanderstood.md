# #932 - as_uint64 can be misanderstood [Closed]

> Username: RoyBellingan  
> Created at: 2023-08-18 18:49:26 UTC  
> Updated at: 2023-10-27 18:03:41 UTC  
> Closed at: 2023-10-27 18:03:41 UTC  
> Url: https://github.com/boostorg/json/issues/932  

Would you considered adding a link to the doc that explain why as_uint64 will fail except when the number is bigger than INT64_MAX ?  
Is at the bottom of the doc, and else is not very clear why  
```  
	auto raw = R"({  
 "x" : 1  
})";  
  
	auto       jv = bj::parse(raw);  
	auto       x  = jv.as_object()["x"].as_uint64();  
  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'   
what(): value is not a std::uint64_t number [boost.json:34 at ../boost_json/include/boost/json/value.hpp:2675:54 in function 'ui  
nt64_t& boost::json::value::as_uint64()']  
```  
  
ATM the function is here https://github.com/boostorg/json/blob/e084a2918cc121528bcf58e04d026f5cbb89e262/include/boost/json/value.hpp#L2673  
  
Maybe just a note to add that should be used normally but instead to_number.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-08-23 14:19:47 UTC  
> Url: https://github.com/boostorg/json/issues/932#issuecomment-1690054020  

It might be time for us to reevaluate our treatment of int64/uint64 and allow accessing int64 as uint64 and vice versa.  
  
This is not undefined behavior because https://eel.is/c++draft/basic.lval#11.2 allows it, although it may require using an uint64 field in the union in both cases (because accessing an inactive field is still UB, even if the type matches.)

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-09-11 14:13:21 UTC  
> Url: https://github.com/boostorg/json/issues/932#issuecomment-1713975213  

What would be the benefit, though? Users would have to check `kind()` anyway, and handle the other kinds someway.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-09-11 14:17:27 UTC  
> Url: https://github.com/boostorg/json/issues/932#issuecomment-1713983180  

You don't need to check `kind()` before using `as_` functions.

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-09-11 15:46:50 UTC  
> Url: https://github.com/boostorg/json/issues/932#issuecomment-1714149890  

So, what do you think should be the behaviour for  
```c++  
value jv = -1;  
std::cout << jv.as_uint64() << '\n';  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2023-09-11 16:17:55 UTC  
> Url: https://github.com/boostorg/json/issues/932#issuecomment-1714200548  

Throws.  
  
Getting this "right" is a bit tricky, but I think it can be done. as_int64 throws for uint64 values that don't fit into int64. as_uint64 throws for negative int64 values. The const overloads just return a const reference. The non-const overloads adjust the kind to match the called function (if necessary) and then return a non-const reference.

---

## Comment 6

> Username: grisumbras  
> Created at: 2023-09-11 16:20:41 UTC  
> Url: https://github.com/boostorg/json/issues/932#issuecomment-1714204523  

That's just a limited version of `to_number`. Why not use `to_number` then?

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-09-11 16:21:12 UTC  
> Url: https://github.com/boostorg/json/issues/932#issuecomment-1714205305  

Also, I'm not sure how I feel about `as_int64` changing the value's kind.
