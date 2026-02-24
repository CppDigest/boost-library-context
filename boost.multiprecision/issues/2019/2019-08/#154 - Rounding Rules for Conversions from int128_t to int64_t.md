# #154 - Rounding Rules for Conversions from int128_t to int64_t [Closed]

> Username: lazylogik  
> Created at: 2019-08-28 09:47:22 UTC  
> Updated at: 2020-03-11 18:52:19 UTC  
> Closed at: 2020-03-11 18:52:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/154  

It seems Rounding Rules for conversion from higher precision to lower precision doesn't seem to be working as per description at https://www.boost.org/doc/libs/1_71_0/libs/multiprecision/doc/html/boost_multiprecision/tut/rounding.html  
  
  
cpp_int | Conversions from integer types are exact if the target has sufficient precision, otherwise they truncate to the first 2^MaxBits bits (modulo arithmetic). Conversions from floating-point types are truncating to the nearest integer.  
-- | --  
  
  
so for following code  
  
  
    boost::multiprecision::int128_t number = INT64_MIN;  
  
    number = number - 1;  
  
    int64_t number2 = number.convert_to<int64_t>();  
  
What is expected to be value of number2? shouldn't it be INT64_MAX? currently it is INT64_MIN.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-03-11 18:52:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/154#issuecomment-597807612  

Sorry to take so long to answer this.... the table you refer to is for conversions *from* a native type.  Conversions *to* a native type are always saturating on overflow, so the result in this case really should be INT64_MIN.
