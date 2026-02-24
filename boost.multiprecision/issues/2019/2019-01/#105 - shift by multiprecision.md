# #105 - shift by multiprecision [Closed]

> Username: ohhmm  
> Created at: 2019-01-20 11:21:39 UTC  
> Updated at: 2019-01-20 16:35:38 UTC  
> Closed at: 2019-01-20 16:35:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/105  

`arbitrary = arbitrary << arbitrary_too;`  
  
Invalid operands to binary expression ('omnn::math::Integer::base_int' (aka 'number<cpp_int_backend<> >') and 'const omnn::math::a_int' (aka 'const number<cpp_int_backend<> >'))

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-20 12:46:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/105#issuecomment-455863664  

That's deliberately not supported - I couldn't think of a use case where shifting by a multiprecision type made any sense - since any conceivable shift value can be stored in a std::size_t.  
Do you have a use case in mind?

---

## Comment 2

> Username: ohhmm  
> Created at: 2019-01-20 16:35:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/105#issuecomment-455881244  

the case is not using numeric_cast for the operand
