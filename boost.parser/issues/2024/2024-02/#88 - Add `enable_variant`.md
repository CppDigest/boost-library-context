# #88 - Add `enable_variant`? [Closed]

> Username: akrzemi1  
> Created at: 2024-02-01 20:26:13 UTC  
> Updated at: 2024-02-04 22:36:56 UTC  
> Closed at: 2024-02-04 22:36:56 UTC  
> Url: https://github.com/boostorg/parser/issues/88  

Is it possible to add a customization point that would make Boost.Parser recognize `boost::variant` as a variant type?  
In a similar vein as `enable_optional` works.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-01 23:32:49 UTC  
> Url: https://github.com/boostorg/parser/issues/88#issuecomment-1922478548  

Enable optional is necessary, because I need to know if a particular value os optional-wrapped or not.  Not so for variants.  This just works already.  There are even tests for it.  See https://github.com/tzlaine/parser/blob/master/test/tuple_aggregate.cpp .

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-02 05:40:35 UTC  
> Url: https://github.com/boostorg/parser/issues/88#issuecomment-1922870616  

However,  this seems to break on more complicated cases. The following program does not compile.  
https://godbolt.org/z/93ddv1To9  
  
But when I switch to `std::variant` from `boost::variant` at line 10. It starts working.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-02-02 05:43:21 UTC  
> Url: https://github.com/boostorg/parser/issues/88#issuecomment-1922874102  

Interesting!  Ok, I'll investigate.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-02-02 23:52:01 UTC  
> Url: https://github.com/boostorg/parser/issues/88#issuecomment-1924915271  

So, there are only 4 errors, and they're all this one or a cascade from this one:  
  
```  
boost/variant/detail/variant_io.hpp:64:14: error: no match for 'operator<<' (operand types are 'std::basic_ostream<char>' and 'const std::vector<SingleRedirection>')  
   64 |         out_ << operand;  
      |         ~~~~~^~~~~~~~~~  
```  
  
This has zero to do with Boost.Parser.  It has to do with Boost.Variant's `op<<`, `std::vector`, and your type.  Am I missing something?

---

## Comment 5

> Username: akrzemi1  
> Created at: 2024-02-03 00:44:10 UTC  
> Updated at: 2024-02-03 00:44:26 UTC  
> Url: https://github.com/boostorg/parser/issues/88#issuecomment-1924946461  

But why would `parse` require an `op<<` of its target?

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-02-03 01:31:07 UTC  
> Url: https://github.com/boostorg/parser/issues/88#issuecomment-1925000520  

Or rather, the question should be why Boost.Parser wants to use `op<<` for `boost::variant` but doesn't want to use it for `std::variant`?  
  
Here's a reduced example: https://godbolt.org/z/rsW4jd1Kd

---

## Comment 7

> Username: tzlaine  
> Created at: 2024-02-03 01:43:44 UTC  
> Url: https://github.com/boostorg/parser/issues/88#issuecomment-1925008886  

Ah, damn.  You're right.  There is one place that is using an `is_variant` trait, and it's in the printing.  Ok, I'll have to add `enable_variant` after all.
